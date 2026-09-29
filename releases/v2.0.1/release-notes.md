# Scanner SDK Mobile Release 2.0.1

## Fixes

Android BLE discovery now snapshots each Service Data UUID and payload while visiting the map entry. This avoids advertisement processing failures when Android `ArrayMap` entry collections reject `toArray()`, and prevents mismatched entries when an iterator reuses its `Map.Entry` object. Payload bytes are copied before asynchronous delivery.

Regression tests cover empty data, independent payload copies, unsupported entry-array conversion, and iterator entry reuse.

## Compatibility

Product version is `2.0.1`. Mobile public APIs and native C ABI `1.0.0` (`0x010000`) remain unchanged. Use matching wrapper and native binary versions. The all-device BLE discovery behavior introduced in 2.0.0 remains in place; apps continue to choose their own candidates. Device capability profiles are unchanged.

Android and iOS Quick Start and Full Demo sources use version `2.0.1`, build `16`. This mobile release does not publish APK, AAB, IPA, TestFlight, .NET/NuGet, or Linux preview packages.

## Known limitations

Platform permissions, hardware, and OS policies affect discovery results and available advertisement fields. Trigger-scan commands still require a matching binary ACK; receiving a barcode does not acknowledge a command.

RW185 long-barcode boundaries and unset `MasterReplaceRule` handling retain the limitations documented for 2.0.0. This patch does not establish additional verified device support.

On the tested `CS7501` combination with main firmware `bd3rCS_RFSBTWD45hb_G616p2` and hardware `GD32F350`, the following items remain classified as a device/firmware limitation:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

The scope must not be generalized to other firmware, hardware, or scanner models.
