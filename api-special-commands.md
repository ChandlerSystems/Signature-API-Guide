# Special Commands

## Device Reset

The device reset command will tell the Bluetooth device to gracefully disconnect. This is useful for devices that like to hang on to the Bluetooth connection longer than it should. It's recommended that this be sent right before disconnecting.

**Command**: `R`

```dart
// Example Pseudo Code

Future<void> disconnect() async {
    writeRaw(['R'.codeUnitAt(0)]);
    await Future.delayed(const Duration(milliseconds: 150)); // Allow the Bluetooth device to fully disconnect
    await disconnectFromDevice(); // Allow the app / device to disconnect as well
}
```
