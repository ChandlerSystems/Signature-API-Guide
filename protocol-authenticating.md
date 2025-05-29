# Authenticating

To authenticate with the valve requires the following steps:

1. Sending an ID status packet
2. Sending the authentication token packet

## ID Status Packet

The ID packet puts the Bluetooth device into a state where it's expecting an authentication request and it will also send some initial data. This must be sent right after the initial connection.

```dart
// Note that this is a status packet, so no header or CRC is expected

List<int> idPacket() => [ 0x3A ];
```

## Authentication Token Packet

The next step is to send the authentication token. The authentication tokens are generated when enabling the API from the app, so you must get the API token from the app. The tokens are generated UUIDs and they get generated every time it's enabled. So, if you want to generate a new token, you can simply disable the API and then enable it once again. Also, since they're UUIDs they will be unique to every device.

The authentication token packet should be sent as raw data, just like you would a status packet.

**Example Auth Token**: `8dffdfbd-40cc-4b86-8df8-1d48463b20f1`

```dart
List<int> uuidToBytes(String uuid) {
  final cleanedUuid = uuid.replaceAll('-', '');
  return [for (int i = 0; i < cleanedUuid.length; i += 2) int.parse(cleanedUuid.substring(i, i + 2), radix: 16)];
}

List<int> authPacket = uuidToBytes('8dffdfbd-40cc-4b86-8df8-1d48463b20F1');
```

**Note**
The auth token can be sent with the dashes. However, the dashes are not valid radix 16 (aka, hexadecimal), so the `int.parse` would throw an exception in this case if the dashes were included.

## Example Successful Data Flow

```text
>>> = App / device write to Bluetooth device
<<< = Bluetooth device write to app / device

>>> 0xEA
<<< 0xCC
<<< [Some initial data that's sent to the app / device]
>>> 0xCC
>>> [0x8D, 0xFF, 0xDF, 0xBD, 0x40, 0xCC, 0x4B, 0x86, 0x8D, 0xF8, 0x1D, 0x48, 0x46, 0x3B, 0x20, 0xF1]
    Note: Notice there is no 0xCC ACK response here
<<< [Rest of the Bluetooth device data]
```

## Example Failure Data Flow

```text
>>> = App / device write to Bluetooth device
<<< = Bluetooth device write to app / device

>>> 0xEA
<<< 0xCC
<<< [Some initial data that's sent to the app / device]
>>> 0xCC
>>> [0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF, 0xBE, 0xEF]
<<< [0xC0, 0x7B, 0x22, 0x61, 0x73, 0x22, 0x3A, 0x31, 0x7D, 0x9F, 0x38]
>>> 0xCC
<<< 0xE0
>>> 0xF0
<<< 0xE0
>>> 0xF0
...
<<< 0xE0
>>> 0xF0
<<< 0xE0
>>> 0xF0
```

This data `0xCC` is an ACK status packet acknowledging the ID packet.

This data `[0xC0, 0x7B, 0x22, 0x61, 0x73, 0x22, 0x3A, 0x31, 0x7D, 0x9F, 0x38]` when converted to ASCII is `.{"as":1 }.8`. If we exclude the header and the two CRC bytes, we can see that the JSON response is `{"as":1}`. This means the authentication state is still unauthorized, which means the authentication failed.

This data `0xE0` is a keep-alive marco status packet. The `0xF0` is the app / device responding with a polo status packet so the Bluetooth device knows the connection is still OK. This is default behavior as the app would retry the provided password again and then prompt for the password should the retry fail.

The Bluetooth device will keep the connection going with keep-alive packets until either the app / device disconnects, or a valid auth token is transmitted.

**Note**
You will notice that the Bluetooth device transmits some initial data regardless of whether the app / device is authenticated or not. This is fine as it's write-only data at this point and it's generic data that would normally be used for app functionality.
