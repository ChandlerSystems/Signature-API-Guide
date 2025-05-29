# CRC-16 Implementation

The following is an example of the CRC-16 algorithm used to compute the checksums for the packets. This example is in Dart, but can easily be written in any other language.

```dart
@immutable
class CsCrc16 {
  static const int defaultSeed = 0xFFFF;

  ///
  /// Method to compute the CRC-16 checksum.
  ///
  /// **Parameters**
  /// - [buffer]: The buffer to compute the CRC-16 checksum for.
  /// - [seed]: The initial value of the CRC-16 checksum.
  ///
  /// **Returns**
  /// - The computed CRC-16 checksum if a valid buffer is provided; otherwise, 0.
  ///
  /// **Note**
  /// This algorithm was adopted from the CRC-16-CCITT algorithm that was originally written in C by Intel. This is one
  /// of the implementations included in Zephyr.
  ///
  /// Copyright (c) 2017 Intel Corporation
  /// SPDX-License-Identifier: Apache-2.0
  ///
  static int compute(List<int> buffer, [int seed = defaultSeed]) {
    if (buffer.isEmpty) {
      return 0;
    }

    int crc = seed;

    for (final byte in buffer) {
      crc = (crc >> 8 | (crc << 8)) & 0xFFFF;
      crc ^= byte & 0xFF;
      crc ^= (crc & 0xFF) >> 4;
      crc ^= (crc << 12) & 0xFFFF;
      crc ^= (crc & 0xFF) << 5 & 0xFFFF;
    }

    return crc & 0xFFFF;
  }

  ///
  /// Method used to verify that a transmitted CRC had no transmission error by checking the residue. The residue for
  /// algorithm used (CRC-16/KERMIT aka CRC-16/CCITT) will always be 0x0000. If there was an error then the value will
  /// be something other than 0x0000.
  ///
  /// See https://reveng.sourceforge.io/crc-catalogue/16.htm for more information.
  ///
  /// **Example**
  /// ```dart
  /// final List<int> testVector = [0x31, 0x32, 0x33, 0x34, 0x35, 0x36, 0x37, 0x38, 0x39];
  /// final int crc = CsCrc16.compute(testVector);
  /// final bool valid = CsCrc16.verify(crc, testVector);
  /// debugPrint('CRC Valid: ${valid ? 'Yes' : 'No'}'); // "CRC Valid: Yes"
  /// ```
  ///
  /// **Parameters**
  /// - [crc]: The CRC value to check.
  /// - [buffer]: The original message that the CRC was computed from.
  /// - [seed]: Optional parameter that can be used to specify the seed value used to compute the CRC.
  ///
  /// **Returns**
  /// - True if the CRC was valid for the specified buffer; otherwise, false.
  ///
  static bool verify(int crc, List<int> buffer, [int seed = defaultSeed]) {
    final int computedCrc = compute(buffer, seed);

    if (crc != computedCrc) {
      final expected = '[Expected: 0x${crc.toRadixString(16).toUpperCase()} ($crc)]';
      final actual = '[Actual: 0x${computedCrc.toRadixString(16).toUpperCase()} ($computedCrc)]';
      debugPrint('[CRC-16] Invalid CRC $expected $actual');
      return false;
    }

    final List<int> bufferWithCrc = List<int>.from(buffer)..addAll([crc >> 8 & 0xFF, crc & 0xFF]);
    final int residue = compute(bufferWithCrc, seed);

    return residue == 0x0000;
  }
}

```
