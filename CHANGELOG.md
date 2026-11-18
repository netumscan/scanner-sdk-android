# Scanner SDK Mobile Release 2.2.0

## Changes

- Android and iOS isolate callbacks and cleanup by connection so late events from a disconnected device cannot affect a replacement session. The runtime matches fragmented module replies to the active request and delivers barcode events separately.
- `refreshInfo()` recognizes additional 2026 RF1 and MH22xx firmware version formats.
- A complete `nul` response for an unset `MasterReplaceRule` returns an available empty object. A timeout still reports a failed read.

## Compatibility

The native C ABI remains `1.0.0` (`0x010000`). Use the wrapper and native binary from the same SDK version. Android and iOS BLE discovery continues to return all devices reported by the platform. Apps choose their own connection candidates.

Android and iOS Quick Start and Full Demo sources use version `2.2.0`, build `17`.

## Known limitations

Platform permissions, hardware, and OS policies affect discovery and available advertisement fields. A trigger-scan command requires a matching binary ACK. RW185 long-barcode limits still depend on the device and firmware.

On the tested `CS7501` combination with main firmware `bd3rCS_RFSBTWD45hb_G616p2` and hardware `GD32F350`, the following Full Read timeouts remain classified as a device/firmware limitation:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

These results apply to the listed firmware and hardware combination.
