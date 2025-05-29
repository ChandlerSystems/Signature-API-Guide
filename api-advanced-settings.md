# Advanced Settings API

| Name                      | Key  | Type    | Unit    | Min | Max  | Access      | Description                                                |
|:--------------------------|:-----|:--------|:--------|:----|:-----|:------------|:-----------------------------------------------------------|
| Days Until Regen          | asd  | Integer | Days    | -   | -    | Write-Only  | The number of days before a regeneration occurs.           |
| Regen Day Override        | asr  | Integer | -       | 0   | 29   | Read/Write  | Maximum number of days between regenerations.              |
| Auto Reserve Mode         | asar | Integer | -       | 0   | 1    | Read/Write  | Flag to indicate whether or not to use auto reserve mode.  |
| Reserve Capacity          | asrc | Integer | Gallons | 0   | 49   | Read/Write  | The current reserve capacity.                              |
| Reserve Capacity Gallons  | asrg | Integer | Gallons | -   | -    | Write-Only  | The total number of reserve capacity gallons.              |
| Total Grains Capacity     | astg | Integer | GPG     | 0   | 399  | Read/Write  | The total grains capacity.                                 |
| Aeration Days             | asad | Integer | Days    | 0   | 9    | Read/Write  | The current aeration days.                                 |
| Chlorine Pulses           | ascp | Integer | -       | 0   | 4    | Read/Write  | The current number of chlorine pulses.                     |
| Display Off               | asdo | Integer | -       | 0   | 1    | Read/Write  | Flag to indicate whether or not the display is off.        |
| Number Of Regen Positions | asnp | Integer | -       | -   | -    | Write-Only  | The total number of regeneration positions.                |
| Cycle Position Times      | aspt | Integer | -       | 0   | 199  | Read/Write  | The cycle position times.                                  |

## Total Grains Capacity

The total grains value should be multiplied by 1,000 to get the actual value. For instance, if this setting is 25, then it actually represents 25,000.

## Auto Reserve Mode Value

- `0`: Manual Mode
- `1`: Automatic Mode

## Reserve Capacity Gallons

The reserve capacity gallons value should be converted to a floating-point value and it's in hundredths.

## Cycle Position Times

The first value should not be modified, if it is, then it will be ignored as that's the service position.

**NOTE**
If the cycle position time is in salt pounds, then the maximum value is 99 lbs.
