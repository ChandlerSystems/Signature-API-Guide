# Authentication API

| Name                  | Key  | Type    | Min | Max  | Access      | Description                                      |
|:----------------------|------|---------|-----|------|-------------|:-------------------------------------------------|
| Authentication State  | as   | Integer | 0   | 2    | Write-Only  | The current authentication state.                |
| Change Password       | acp  | Integer | 0   | 9999 | Read-Only   | Changes the Bluetooth password on the device.    |

## Authentication State Values

The authentication state of the device will be one of the following values:

- `0`: Unknown
- `1`: Not Authenticated
- `2`: Authenticated
