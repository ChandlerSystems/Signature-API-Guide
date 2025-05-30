# Important Notes

- :warning: You must be running firmware C6.13+. It is not supported on any other firmware versions.
- The Bluetooth device was designed to only send data packets as the data changes on the device. If there are no current changes, then the valve will only send keep-alive status packets, to which you must respond.
- The Bluetooth device may stop responding to packets or connections during development. If this happens, just reboot the Bluetooth device to restore functionality.
- There are some values transmitted from the Bluetooth device for keys not documented in this guide. Those values are unused and should be ignored.
- The firmware is the primary, or dominate, role when it comes to communicating. There should never be a need to transmit a keep-alive status packet, a timeout status packet, etc. to the Bluetooth device. It will send those to the app / device when needed, just make sure the app / device responds properly.
- The Bluetooth device will ignore any packets for the following reasons below.
  - Any commands it does not recognize
  - It does not allow changes for the data sent
  - It received the same value it already has. For instance, if the Bluetooth device currently has 25% for the reserve capacity and you send it a packet trying to set it as 25%, then it will be ignored as there would be no changes.
