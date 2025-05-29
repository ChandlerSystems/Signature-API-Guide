# Graphs API

| Name                        | Key   | Type      | Unit    | Min | Max | Access      | Description                                                        |
|:----------------------------|-------|-----------|:--------|-----|-----|-------------|:-------------------------------------------------------------------|
| Peak Flow Daily             | grp   | [Integer] | GPM     | -   | -   | Write-Only  | Graph that shows the peak water flow per day.                      |
| Gallons Used Daily          | ggd   | [Integer] | Gallons | -   | -   | Write-Only  | Graph that shows the number of gallons used per day.               |
| Gallons Used Between Regens | ggr   | [Integer] | Gallons | -   | -   | Write-Only  | Graph that shows the number of gallons used between regenerations. |

**NOTE**
All graph values are in hundredths, so each value needs divided by 100.
