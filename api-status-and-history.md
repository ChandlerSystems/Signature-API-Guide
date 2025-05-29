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

## Gallons Since Last Regen

The gallons since last regen value should be converted to a floating-point value and it's in hundredths.

## Total Gallons

The total gallons value should be converted to a floating-point value and it's in hundredths.

## Total Gallons Resettable

The total gallons resettable value should be converted to a floating-point value and it's in hundredths.

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
