# Status & History API

| Name                     | Key  | Type    | Unit    | Min | Max  | Access      | Description                                                           |
|:-------------------------|------|---------|:--------|-----|------|-------------|:----------------------------------------------------------------------|
| Days In Operation        | shdo | Integer | Days    | -   | -    | Write-Only  | The number of days the Bluetooth device has been in operation.        |
| Days Since Last Regen    | shdr | Integer | Days    | -   | -    | Write-Only  | The number of days since the last regeneration.                       |
| Gallons Since Last Regen | shgs | Integer | Gallons | -   | -    | Write-Only  | The number of gallons flowed since the last regeneration.             |
| Regen Counter            | shrc | Integer | -       | -   | -    | Write-Only  | The number of regenerations that have completed.                      |
| Regen Counter Resettable | shrr | Integer | -       | -   | -    | Read/Write  | The number of regenerations that have completed since the last reset. |
| Total Gallons            | shgt | Integer | Gallons | -   | -    | Write-Only  | The total number of gallons that have flowed.                         |
| Total Gallons Resettable | shgr | Integer | Gallons | -   | -    | Read/Write  | The total number of gallons that have flowed since the last reset.    |
| Error Logs               | shel | Integer | -       | -   | -    | Write-Only  | A log of errors that have occurred (last 20 errors).                  |

## Gallon Values

The gallons since last regen (`shgs`), total gallons (`shgt`) and total gallons resettable (`shgr`) values should be converted to floating-point values. How they're sent depends on the firmware version (see the firmware version in the [device list](api-device-list.md) section):

- **C6.19 and newer:** The values are sent as quarter gallon counts, so each value needs divided by 4.
- **C6.18 and older:** The values are sent in hundredths, so each value needs divided by 100.

```dart
double gallons(int value) => firmwareVersion >= 619 ? value / 4.0 : value / 100.0;

// Examples
// C6.19: 4939 / 4 = 1,234.75 gallons
// C6.18: 123475 / 100 = 1,234.75 gallons
```

## Error Logs

The Bluetooth device will remember the last 20 errors that have occurred. It will send all 20 entries regardless of whether there was an error or not. Each error log entry is a JSON object and the following shows how the objects are defined:

- `d`: Days In Operation
- `h`: Time the error occurred (hours)
- `m`: Time the error occurred (minutes)
- `s`: Time the error occurred (seconds)
- `e`: The error that occurred (see the error state values list in the [global](api-global.md) section)

**Example JSON**

```json
{
  "shel": [
    {
      "d": 11,
      "h": 1,
      "m": 8,
      "s": 45,
      "e": 6
    },
    {
      "d": 0,
      "h": 0,
      "m": 0,
      "s": 0,
      "e": 0
    },
    ...
    {
      "d": 0,
      "h": 0,
      "m": 0,
      "s": 0,
      "e": 0
    }
  ]
}
```
