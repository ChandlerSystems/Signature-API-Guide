# Bluetooth Packet Structure

## Overview

The following is a simplified view of the packet structures that are used. There are three types of packets used, a raw packet, a status packet, and a data packet.

## Raw Packet

Raw packets are used for [authenticating](protocol-authenticating.md) and [special commands](api-special-commands.md). Refer to those sections for more information.

## Status Packet

Status packets contain a single byte. These packets are used to communicate a state or response and they contain no data.

```text
 ┌──────────┐
 │  HEADER  │
 ├──────────┤
 │   0xHH   │
 └──────────┘
 ```

### Status Packet Header

See the [headers](api-headers.md) section for more details.

## Data Packets

Data packets contain a single byte as the header, a variable amount of JSON data, and two bytes at the end that represents the checksum of the packet.

```text
 ┌──────────┬───────────────────┬──────────────┐
 │  HEADER  │     JSON DATA     │   CHECKSUM   │
 ├──────────┼───────────────────┼──────────────┤
 │   0xHH   │  0x7B, ..., 0x7D  │  0xCC, 0xCC  │
 └──────────┴───────────────────┴──────────────┘
 ```

### Data Packet Header

See the [headers](api-headers.md) section for more details.

### JSON Data

The JSON data should start with the standard 0x7B byte (i.e., **{**) and end with the 0x7D byte (i.e., **}**). The rest is variable depending on the data being sent.

### Checksum

The checksum is the CRC of the header and the JSON data. See below for an example of how to validate the packets and also see the [CRC-16 implementation](other-crc16.md) section for the CRC algorithm used.

## Receiving Packets

The checksum should be verified that it's valid using the checksum verify method (see the [CRC-16 Implementation](other-crc16.md) section). This verify method will validate the packet data and the CRC checksum itself. If the checksum is valid and the data is valid, then the data can be trusted and accepted. An ACK (acknowledgement) status packet should be sent back to the Bluetooth device to indicate this. If the CRC validation check fails, then a NAK (negative acknowledgement) status packet needs to be sent to indicate the packet is malformed.

### Min Data Packet Size

The following is the minimum data packet size, or length. This needs checked before doing anything with the data to avoid any exceptions from accessing an array element that does not exist.

```dart
static const int headerSizeBytes = 1;
static const int crcSizeBytes = 2;
static const int emptyJsonSizeBytes = 2; // '{}'
static const int headerAndCrcSizeBytes = headerSizeBytes + crcSizeBytes;
static const int minDataPacketSize = headerAndCrcSizeBytes + emptyJsonSizeBytes;

// NOTE
// We could just use 5 while checking the length of the packet, but it's best to
// define constant values so there are no "magic numbers". Having variables named
// headerSizeBytes, crcSizeBytes, etc. means more than '5'. Also, the constants
// can be used elsewhere in the project as well. For instance, when 'chunking'
// the packets the header and CRC size bytes are needed. So, this also provides
// a source of truth.
```

#### Example Packet Validation

```dart
if (packet.length < minDataPacketSize) {
    // Invalid packet length, a NAK packet should be sent.
    return;
}

final dataSize = packet.length - crcSizeBytes;
final newPacket = packet.sublist(0, dataSize);
final expectedCrc = (packet[packet.length - 1] << 8) | packet[packet.length - crcSizeBytes];

if (!CsCrc16.verify(expectedCrc, newPacket)) {
    // The CRC is invalid, a NAK packet should be sent.
    return;
}

// Packet data is good, the JSON data can be parsed now.
```

### Sending Packets

When sending data packets, you must CRC the header **and** the data, just as shown when receiving a packet. Once you have the checksum you must append it to the end of the packet.
