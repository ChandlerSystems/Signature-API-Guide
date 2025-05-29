# Packet Headers

Below are the following acceptable header values that are used by the Bluetooth packets:

```dart
class CsBleProcessorHeaders {
  ///
  /// Bit to indicate that this is the first packet in a series of packets.
  ///
  static const firstPacket = 0x80;

  ///
  /// Bit to indicate that this is the last packet in a series of packets.
  ///
  static const lastPacket = 0x40;

  ///
  /// Bit to indicate that this is a keep-alive response.
  ///
  static const keepAlive = 0x20;

  ///
  /// Bit to indicate the type of keep-alive response.
  ///
  static const keepAliveType = 0x10;

  ///
  /// Bit to indicate that this is an ACK/NAK response.
  ///
  static const ackNak = 0x8;

  ///
  /// Bit to indicate the type of ACK/NAK response.
  ///
  static const ackNakType = 0x4;

  ///
  /// Bit to indicate that this is a timeout response.
  ///
  static const timeout = 0x2;

  ///
  /// Bit to indicate the type of timeout response.
  ///
  static const timeoutType = 0x1;

  ///
  /// The NOP (no operation) response.
  ///
  static const nop = 0x0;

  ///
  /// Bits to indicate that there is only a single packet in the response.
  ///
  static const singlePacket = firstPacket | lastPacket;
}
```
