# Device List API

| Name                    | Key   | Type    | Unit    | Min | Max | Access      | Description                                                        |
|:------------------------|-------|---------|:--------|-----|-----|-------------|:-------------------------------------------------------------------|
| Serial Number A         | dlsa  | Integer | -       | -   | -   | Write-Only  | The first half of the serial number.                               |
| Serial Number B         | dlsb  | Integer | -       | -   | -   | Write-Only  | The second half of the serial number.                              |
| Firmware Version        | dlf   | Integer | -       | -   | -   | Write-Only  | The current firmware version                                       |
| Regen Motor In Progress | dlr   | Boolean | -       | 0   | 1   | Write-Only  | Flag to indicate if the motor is moving to a regeneration position.|

## Serial Number Values

The serial number is split into two `unsigned 32-bit` parts: `dlsa` (first half) and `dlsb` (second half). The serial number is concatenated displayed as a hexadecimal string; below is an example Dart code illustrating this.

```dart
String getSerialNumber() => '${serialNumberA.toRadixString(16)}${serialNumberB.toRadixString(16)}'.toUpperCase();

// Example
// Serial Number A: 885281167
// Serial Number B: 166204050
// Final Serial Number: 34C4518F9E81292
```

## Firmware Version

The firmware version comes across as a raw integer value. You can use the following to convert it to the firmware version that's used for display (i.e., C6.13).

```dart
String firmwareVersion() => 'C${firmwareVersion ~/ 100}.${firmwareVersion % 100}';

// Example
// 613 = C6.13
```
