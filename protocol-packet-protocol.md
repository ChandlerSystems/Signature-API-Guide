# Packet Protocol

First, you need to review the [packet structure](protocol-packet-structure.md), if you haven't already, to get a better understanding of how the packets are structured.

As mentioned in the [bluetooth communication](protocol-communication.md) section, the MTU plays a large role in sending the packets as you want the packets to never exceed the negotiated MTU. Here are a few examples of what this would look like:

## Sending A Single Packet

**Negotiated MTU**: `251`
**Packet Size**: `200`

Since the MTU is larger than the packet size, we can send this data in one packet. Here what that would look like:

- **Packet 1**: [0xC0, 0x7B, ...[JSON Data]... 0x7D, 0x??, 0x??]

As you can see, the header is `0xC0`. This is the `first packet (0x80)` header bitwise ORed together with the `last packet (0x40)` header (aka a single packet since it's the first and the last). Then you have the JSON data that's associated with the packet along with the two CRC bytes (0x??).

## Sending Multiple Packets

**Negotiated MTU**: `251`
**Packet Size**: `921`

As you can see, we have a packet that contains 921 bytes, but we only negotiated 251 bytes. This means that the packet will need `chunked`, meaning it needs cut up into multiple smaller packets. The number of packets we need can be determined by the following:

```dart
// We need to take into consideration that we have a header and two CRC bytes as those need included too. So, the
// true packet size will be your total data minus the header and CRC bytes. We also need to account for those bytes
// on every proceeding packet as well. So, we need to determine the number of packets and multiply that by the number
// of header and CRC bytes.

final headerAndCrcSizeBytes = 3;
final negotiatedMtu = min(device.negotiatedMtu, 251 /* max packet size */);
final packetMtu = negotiatedMtu - headerAndCrcSizeBytes;
final headerCrcBytes = packet.length / packetMtu.toDouble() * headerAndCrcSizeBytes;
final totalPackets = ((packet.length + headerCrcBytes) / negotiatedMtu).ceil();
```

Using this formula, we can see that there will be `4` total packets in our example:

- **negotiatedMtu**: `251`
- **packetMtu**: `249`
- **headerCrcBytes**: `11.0963855421686746988`
- **totalPackets**: `4`

Here is what each packet will look like (note that the last two bytes on each packet are the checksum bytes):

- **Packet 1**: [0x80, 0x7B, ...[JSON Data]... 0x7D, 0x??, 0x??]
  - 0x80 = First Packet
- **Packet 2**: [0x00, 0x7B, ...[JSON Data]... 0x7D, 0x??, 0x??]
  - 0x00 = NOP
- **Packet 3**: [0x00, 0x7B, ...[JSON Data]... 0x7D, 0x??, 0x??]
  - 0x00 = NOP
- **Packet 4**: [0x40, 0x7B, ...[JSON Data]... 0x7D, 0x??, 0x??]
  - 0x40 = Last Packet

As you can see, the first packet has the `first packet (0x80)` header. The second and third packet uss the `NOP (0x00)` header. The nop header let's the Bluetooth device know that there are one or more packets to follow. The final packet has the `last packet (0x40)`, which tells the Bluetooth device this is the end of the packet.

## Receiving Packets

Receiving packets from the Bluetooth device is the same as sending them. The only difference is you need to handle the single and multiple packets as they come in.

This is why the `first packet (0x80)` and `last packet (0x40)` headers are important. They not only tell the Bluetooth device about what to expect, but you can use these headers to manage your buffers. For instance, consider the following example that would work with receiving single and multiple packets:

```dart
// This is just an example, it has not been test!

final List<int> _receiveBuffer = {};

Future<void> _processDataPacket(List<int> buffer) async {
  if (buffer.length < minDataPacketSize) {
    return
  }

  final header = buffer[0];
  final dataSize = buffer.length - crcSizeBytes;
  final newPacket = buffer.sublist(0, dataSize);
  final expectedCrc = (buffer[buffer.length - 1] << 8) | buffer[buffer.length - crcSizeBytes];

  if (!CsCrc16.verify(expectedCrc, newPacket)) {
    // CRC validation failed, send a NAK status packet
    return;
  }

  if (_isFlagSet(header, CsBleProcessorHeaders.firstPacket)) {
    // This is the first packet, clear any existing receive buffer data
    _receiveBuffer = [];
  }

  // Add the JSON data to the receive buffer
  final packetData = newPacket.sublist(headerSizeBytes, newPacket.length);
  _receiveBuffer.addAll(packetData);

  if (_isFlagSet(header, CsBleProcessorHeaders.lastPacket)) {
    // Send an ACK

    // This is the last packet. We should have the full JSON data now. 
    final jsonString = String.fromCharCodes(_receiveBuffer).trim();
    // Process JSON
  } else {
    // Waiting for more packets, send an ACK to acknowledge this packet is good
  }
}

bool _isFlagSet(int header, int flag) => (header & flag) > 0;
```
