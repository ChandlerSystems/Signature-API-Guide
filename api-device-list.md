# Device List API

| Name                    | Key   | Type    | Unit    | Min | Max | Access      | Description                                                         |
|:------------------------|-------|---------|:--------|-----|-----|-------------|:--------------------------------------------------------------------|
| Serial Number A         | dlsa  | Integer | -       | -   | -   | Write-Only  | The first half of the serial number.                                |
| Serial Number B         | dlsb  | Integer | -       | -   | -   | Write-Only  | The second half of the serial number.                               |
| Firmware Version        | dlf   | Integer | -       | -   | -   | Write-Only  | The current firmware version.                                       |
| Valve Type              | dlvt  | Integer | -       | -   | -   | Write-Only  | The current valve type.                                             |
| Valve Series            | dlvs  | Integer | -       | 2   | 6   | Write-Only  | The current valve series.                                           |
| Regen Motor In Progress | dlr   | Boolean | -       | 0   | 1   | Write-Only  | Flag to indicate if the motor is moving to a regeneration position. |

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

## Valve Type

- `1`: Metered Softener
- `2`: Timeclock Softener
- `3`: Metered Softener
- `4`: Backwashing Filter
- `5`: Backwashing Filter
- `6`: HydroxR Filter
- `7`: ReactR Filter
- `8`: Ultra Filter
- `9`: Aeration Filter
- `10`: Aeration Filter
- `11`: Aeration Filter
- `12`: Aeration Filter
- `13`: Aeration Filter
- `14`: Aeration Filter
- `15`: Aeration Filter
- `16`: Aeration Filter
- `17`: Metered Softener
- `18`: Backwashing Filter
- `19`: Metered Softener
- `20`: Backwashing Filter
- `21`: Metered Softener
- `22`: Backwashing Filter
- `23`: Aeration Filter
- `24`: Aeration Filter
- `25`: Aeration Filter
- `26`: Backwashing Filter
- `27`: Backwashing Filter

**NOTE**
We have various products for various markets that are similar, but not always the same.  The valve type published here is to help identify what type of unit you have to your App/interfacing software, especially if you have more than one unit.

## Valve Series

- `2`: Series 2 (D12 Body)
- `3`: Series 3 (D15 Body)
- `4`: Series 4 (CS125 Body)
- `5`: Series 5 (CS150 Body)
- `6`: Series 6 (CS121 Body)
