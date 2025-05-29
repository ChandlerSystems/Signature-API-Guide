# API Overview

The following are the fields each API definition table has and their meaning.

## Name

This is the friendly name for the packet.

## Key

This is the packet key. It's used to identify the data and is also the key for the JSON data. These are short as possible to minimize the number of bytes being transmitted.

## Type

The expected data type. The valve will not transmit 64-bit integers or floating-point numbers. If a floating point type is expected, then it will be noted. For instance, the current water flow will be transmitted as an integers, but it's really a floating-point number.

```text
Current Flow Example

The current flow (aka present flow), is transmitted as an integer with hundredths precision.

Current Flow: 1524
Actual Current Flow: 1524 / 100.0 = 15.24 GPM
```

If there is an array, then it will have brackets around it (i.e., `[Integer]`).

## Unit

The unit the value is in (i.e., GPM, Gallons, etc.).

## Min

The minimum value allowed, if applicable.

## Max

The maximum value allowed, if applicable.

## Access

- **Write-Only**: When an entry is marked as `Write-Only`, it means that these packets are transmitted to the app / device only. If the app / device tries to send this packet to the Bluetooth device, then it will be ignored.
- **Read-Only**: When an entry is marked as `Read-Only`, it means that these packets are transmitted to the Bluetooth device only. The Bluetooth device will never send these to the app / device.
- **Read/Write**: The app / device or the Bluetooth device will transmit these packets.

## Description

A general description of the API entry.
