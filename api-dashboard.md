# Dashboard API

| Name                               | Key  | Type    | Unit    | Min | Max  | Access      | Description                                                       |
|:-----------------------------------|:-----|:--------|:--------|:----|:-----|:------------|:------------------------------------------------------------------|
| Time - Hours                       | dh   | Integer | -       | 0   | 23   | Read/Write  | The hours component of the valves time (in military time).        |
| Time - Minutes                     | dm   | Integer | -       | 0   | 59   | Read/Write  | The minutes component of the valves time.                         |
| Time - Seconds                     | ds   | Integer | -       | 0   | 59   | Read/Write  | The seconds component of the valves time.                         |
| Battery Level                      | dbl  | Integer | mV      | 0   | 9700 | Write-Only  | The current battery level.                                        |
| Total Gallons Remaining            | dtgr | Integer | Gallons | -   | -    | Write-Only  | The total number of treated water remaining.                      |
| Peak Flow Daily                    | dpfd | Integer | GPM     | -   | -    | Write-Only  | The peak flow rate for the day.                                   |
| Water Hardness                     | dwh  | Integer | GPG     | 0   | 99   | Read/Write  | The water hardness value.                                         |
| Day Override                       | ddo  | Integer | Days    | 0   | 29   | Read/Write  | The day override value.                                           |
| Current Day Override               | dcdo | Integer | Days    | 0   | 29   | Read/Write  | The current day override.                                         |
| Water Used Today                   | dwu  | Integer | Gallons | -   | -    | Write-Only  | The total gallons of water used for the day.                      |
| Average Water Used                 | dwau | Integer | Gallons | -   | -    | Write-Only  | The average water used.                                           |
| Regen Time Hours                   | drth | Integer | Hours   | 0   | 23   | Read/Write  | The regeneration time hours (in military time).                   |
| Regen Time Type                    | drtt | Integer | -       | 0   | 2    | Write-Only  | The regeneration time type.                                       |
| Regen Time Remaining               | drtr | Integer | -       | -   | -    | Write-Only  | The remaining regeneration step time.                             |
| Regen Current Position             | drcp | Integer | -       | -   | -    | Write-Only  | The current regeneration position.                                |
| Regen In Aeration                  | dria | Integer | -       | 0   | 1    | Write-Only  | Flag to indicate whether or not in aeration.                      |
| Regen Soak Mode                    | dps  | Integer | -       | 0   | 1    | Write-Only  | Flag to indicate whether or not in brine soak.                    |
| Regen Soak Timer                   | drst | Integer | -       | -   | -    | Write-Only  | The amount of time remaining, in minutes, for brine soak.         |
| Prefill Enabled                    | dpe  | Integer | -       | 0   | 1    | Read/Write  | Flag to indicate whether or not brine pre-fill is enabled or not. |
| Prefill Duration                   | dpd  | Integer | Hours   | 1   | 4    | Read/Write  | Brine pre-fill duration.                                          |
| Brine Tank - Total Salt Pounds     | dpd  | Integer | Pounds  | 0   | -    | Write-Only  | The total pounds of salt that the configured brine tank can hold. |
| Brine Tank - Remaining Salt Pounds | dpd  | Integer | Pounds  | 0   | -    | Read/Write  | The current remaining salt, in pounds, that is left in the tank.  |

## Battery Level

The battery level, which is in millivolts, should be converted to a floating-point value and it's in thousandths.

```dart
Battery Level: 9220 mV
Battery Level: 9220 / 1000 = 9.2 V
```

## Total Gallons Remaining

The total gallons remaining value should be converted to a floating-point value and it's in hundredths.

## Peak Flow Daily

The peak flow daily value should be converted to a floating-point value and it's in hundredths.

## Water Used Today

The water used today value should be converted to a floating-point value and it's in hundredths.

## Average Water Used

The average water used value should be converted to a floating-point value and it's in hundredths.

## Regen Time Type

- `0`: Seconds
- `1`: Minutes
- `2`: Salt Pounds
  - This defaults to minutes. In the firmware it will display minutes on the display there is less than a minutes, at which point it will start showing seconds countdown.  

## Brine Tank - Remaining Salt Pounds

The remaining salt pounds value should be converted to a floating-point value and it's in tenths. In the apps we convert this value to float-point value, divide it by 10, and then rounds it back to the nearest integer.

```dart
final remainingSaltPounds = ((value as int).toDouble() / 10.0).round();
```

**NOTE**
This value is marked as read/write, but it's recommended to use the app instead of directly setting it here. This value is here in case you want to get a percentage of salt left, set an alert for when the salt gets below a certain percentage, etc.
