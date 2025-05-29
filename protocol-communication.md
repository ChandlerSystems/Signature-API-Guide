# Bluetooth Communication

## Getting Started

Bluetooth communication starts with the following:

- Initial connection to the device
- Discovering the services and characteristics of the device
- Negotiating the MTU

### Initial Connection

The initial connection usually depends on the operating system and/or library being used. As an example, you would connect to a device on iOS using [centralManager.Connect()](https://developer.apple.com/documentation/corebluetooth/cbcentralmanager/connect(_:options:)) and on Android you would connect using [BluetoothDevice.connectGatt()](https://developer.android.com/reference/android/bluetooth/BluetoothDevice#connectGatt(android.content.Context,%20boolean,%20android.bluetooth.BluetoothGattCallback)).

#### Notes

- If you're using Android, it's highly advisable not to deal directly with the BluetoothDevice. I would recommend using [Nordic's Android BLE Library](https://github.com/NordicSemiconductor/Android-BLE-Library) or [RxAndroidBle](https://github.com/dariuszseweryn/RxAndroidBle) as these libraries account for a lot of the issues that exist when working with Bluetooth on Android.
  - If you're using Flutter, then the [flutter_blue_plus](https://github.com/chipweinberger/flutter_blue_plus) library is the recommended library.
- Android is notorious for throwing a generic `code 133` GATT error, no matter what library you use. There is no definite answer or solution to this issue. However, if you get the error, just wait for a short period of time and retry the connection. The best approach is to keep retrying the connection several times while incrementing the delay between retries. Using this method works almost all the time and mitigates the `code 133` issue. The following is a rough pseudo example of how to do this in Dart.

```dart
  int _retryCount = 0;

  Future<bool> connectToDevice(BluetoothDevice device, {int maxRetries = 8}) async {
    while (_retryCount <= maxRetries) {
      try {
        print('Attempt ${_retryCount + 1} to connect to ${device.remoteId}');
        await Future<void>(() async {
          await device.connect();
        });
        return true;
      } catch (e, _) {
        final delay = Duration(milliseconds: 250 * (1 << (++_retryCount - 1)));
        print('Connect error: $e [Retry $_retryCount/$maxRetries in ${delay.inMilliseconds}ms]');
        await Future.delayed(delay);
      }
    }
    return false;
  }
  ```

### Discovering Service & Characteristics

After the initial connection is made, you need to discover the services and characteristics. This lets the core Bluetooth functionality know how to communicate with the Bluetooth device.

Here are the following service and characteristics that are used to communicate:

- **Service UUID**: `a725458c-bee1-4d2e-9555-edf5a8082303`
- **Read Characteristic UUID**: `a725458c-bee2-4d2e-9555-edf5a8082303`
- **Write Characteristic UUID**: `a725458c-bee3-4d2e-9555-edf5a8082303`

### Negotiating MTU

The device you use to connect to the Bluetooth device with (i.e., a phone, tablet, etc.) and the Bluetooth device itself has what's known as an MTU, or Maximum Transmission Unit. The MTU determines how many bytes a packet can be. Both devices have a maximum MTU that can be used. For instance, the Bluetooth standard has the current maximum set to `512 bytes`. However, the Bluetooth device on Nordic's nRF52840 has a maximum MTU of `251 bytes`. The devices connecting, as mentioned, also have a maximum MTU and it's device dependant. Older devices may only have a maximum of 180 bytes, while newer devices have the maximum of 512 bytes. When negotiating the MTU between the devices, the lowest value will be chosen. For instance, if you're using a newer phone that can handle the 512 bytes, it will be negotiated to only 251 bytes since that's all the Bluetooth device can accommodate.

- **iOS**: On iOS, the OS negotiates the MTU for you automatically.
- **Android**: This depends on the library you use. The recommended libraries above require you to manually negotiate the MTU. However, some libraries, like `flutter_blue_plus` for Flutter, will automatically negotiate the MTU for you.

This is important to understand because if you try to send a packet that is 200 bytes, but your device is only capable of 100 bytes, then it depends on the backend as to how the packet gets handled. The write will either fail silently or it will truncate the bytes that exceed the limit. Either way, that will cause a transmission error. See the [packet protocol](protocol-packet-protocol.md) section for more information regarding this.

The only **important thing** to note here, is the MTU needs negotiated and you need to know what that value is for when it comes time to send packets.
