# Global API

| Name                    | Key   | Type    | Unit    | Min | Max | Access      | Description                                                        |
|:------------------------|-------|---------|:--------|-----|-----|-------------|:-------------------------------------------------------------------|
| Valve Status            | gvs   | Integer | -       | -   | -   | Write-Only  | Current valve status bits.                                         |
| Valve Error             | gve   | Integer | -       | -   | -   | Write-Only  | Current valve error.                                               |
| Present Flow            | gpf   | Integer | GPM     | -   | -   | Write-Only  | Current flow in GPM in hundredths.                                 |
| Regen Active            | gra   | Boolean | -       | 0   | 1   | Write-Only  | Flag to indicate whether or not there is an active regeneration.   |
| Regen State             | grs   | Integer | -       | 0   | 13  | Write-Only  | The current regeneration state.                                    |
| Regen Now               | grn   | Boolean | -       | 0   | 1   | Read-Only   | Flag to tell the valve to start a regeneration immediately.        |
| Regen Later             | grl   | Boolean | -       | 0   | 1   | Read-Only   | Flag to tell the valve to start a regeneration at the next time.   |
| Find Home               | gfh   | Boolean | -       | 0   | 1   | Read-Only   | Flag to tell the valve to find home. Ensures valve is at home slot.|

## Valve Status

The valve status is a single value that has certain bits toggled depending on the certain state of the valve.

```text
0b00000000
  │││││││└──► [Not Used]
  ││││││└───► [Not Used]
  │││││└────► [Not Used]
  ││││└─────► [Not Used]
  │││└──────► [Not Used]
  ││└───────► Bypass State
  │└────────► Water State
  └─────────► Salt Status (0 = Salt OK, 1 = Salt Low)
```

**NOTE**
The water state and bypass state or for future use when the new CS/Commercial boards are designed as those boards allow water shutoff and bypass.

## Error State Values

The error state of the device will be one of the following values:

- `0`: No error
- `2`: Lost home
- `3`: No encoder slots seen, normal motor current
- `4`: Can't find home
- `5`: No encoder slots seen, high motor current
- `6`: No encoder slots seen, no motor current
- `7`: TWEDO motor timeout
- `192`: Regen Aborted (On Battery)

**NOTE**
Error 192 is a logging error only. This means it will show up in the error logs, but it will not be displayed on the Bluetooth device's display. See the [status & history](api-status-and-history.md) section for more details on the error log.

## Present Flow Value

The present flow value should be converted to a floating-point value and it's in hundredths. Here is an example:

```dart
String get presentFlow => (glPresentFlow / 100.0).toStringAsFixed(2);

// Example: 568 = 5.68 GPM
```

## Regen State Values

The following regeneration states are transmitted while a regeneration is happening.

- `0`: Idle
- `1`: Moving To Next Position
- `2`: Moving To Final Position
- `3`: TWEDO Waiting For Motor State
- `4`: Waiting For TWEDO State
- `5`: Waiting In Position
- `6`: Moving To Service
- `7`: Moving To Bypass
- `8`: In Bypass
- `9`: Moving To Brine Soak
- `10`: Waiting In Brine Soak
- `11`: Moving To Creep Position
- `12`: Creeping To Position
