# Status Packets

## Writing Status Packets

Status packets do not contain JSON data or CRC bytes, only the header byte. The Bluetooth device expects a singular byte.

## ACK/NAK Status Packet

The ACK/NAK status packet tells the Bluetooth device if it successfully transmitted the packet or not. This should be sent whenever a data packet is received from the Bluetooth device. A NAK should be sent if the packet is malformed; otherwise, the ACK should be sent.

```dart
enum CsBleProcessorAckNakType {
  ///
  /// Negative acknowledgement type for when the packet was not received correctly and it needs resent.
  ///
  nak,

  ///
  /// Acknowledgement type for when the packet was received correctly.
  ///
  ack,
}
```

Here is an example of how the header can be constructed:

```dart
static int createAckNakHeader(CsBleProcessorAckNakType type) =>
      CsBleProcessorHeaders.singlePacket |
      CsBleProcessorHeaders.ackNak |
      (type == CsBleProcessorAckNakType.ack ? CsBleProcessorHeaders.ackNakType : CsBleProcessorHeaders.nop);
```

## Keep-Alive Status Packet

The keep-alive status packet is used when no data is being transferred to keep the connection stable. The Bluetooth radio has a `supervision timeout` that is used to automatically disconnect if communication stops. This will prevent that from happening as long as the app / device continues to marco and polo each other.

```dart
enum CsBleProcessorKeepAliveType {
  ///
  /// Marco type when either the device or the app is sending a keep-alive packet
  ///
  marco,

  ///
  /// Type when either the device or the app is responding to a keep-alive marco packet
  ///
  polo,
}
```

Here is an example of how the header can be constructed:

```dart
static int createKeepAliveHeader(CsBleProcessorKeepAliveType type) =>
      CsBleProcessorHeaders.singlePacket |
      CsBleProcessorHeaders.keepAlive |
      (type == CsBleProcessorKeepAliveType.polo ? CsBleProcessorHeaders.keepAliveType : CsBleProcessorHeaders.nop);
```

## Timeout Status Packet

The timeout status packet is used by the Bluetooth device when it stops receiving keep-alive responses. It's a last attempt to try and keep the connection alive.

```dart
enum CsBleProcessorTimeoutType {
  ///
  /// The timeout type for when the device stops receiving responses from the app.
  ///
  query,

  ///
  /// The response to the query timeout type.
  ///
  ack,
}
```

Here is an example of how the header can be constructed:

```dart
static int createTimeoutHeader(CsBleProcessorTimeoutType type) =>
      CsBleProcessorHeaders.singlePacket |
      CsBleProcessorHeaders.timeout |
      (type == CsBleProcessorTimeoutType.ack ? CsBleProcessorHeaders.timeoutType : CsBleProcessorHeaders.nop);
```
