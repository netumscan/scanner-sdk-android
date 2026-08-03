# Scanner SDK Mobile Release 1.0.0

## Highlights

- Stable Android Kotlin and Apple Swift product-facing APIs.
- BLE GATT discovery, connection, scan events, device information, battery,
  capability entries, semantic commands, and structured failure reporting.
- Native ABI `1.0.0` (`0x010000`) with frozen C symbols and structure layouts.
- Android Maven artifacts contain binary AAR integration materials without
  private wrapper, JNI, protocol-kernel, or adapter sources.
- Demo applications are published as source in
  `netumscan/scanner-sdk-samples`; no APK, AAB, IPA, TestFlight, or app-store
  package is distributed.

## Breaking Migration

- Applications upgrading from `0.x` must use canonical capability and
  setting-code keys returned by the SDK; historical raw keys and aliases are
  not accepted.
- Android callers should inspect `ScannerException.discoveryFailure` for a
  synchronous discovery-start failure and continue observing
  `ScannerSdk.discoveryFailures` for asynchronous failures.
- Android, Apple, and other wrappers must be paired with the `1.0.0` native
  runtime. Mixing `0.x` and `1.0.0` binaries is unsupported.

## Supported Mobile Scope

- Android 8.0+ (`minSdk 26`), BLE GATT primary transport.
- iOS 15+ and macOS 12+, BLE GATT primary transport.
- Android SPP Classic remains a compatibility skeleton and is not part of the
  mobile `1.0.0` support commitment.

## Release Evidence

The release commit, workflow runs, artifact SHA-256 values, Maven coordinate,
SwiftPM URL, and Android/iPhone compatibility records are recorded in the
release assets and point to the same `1.0.0` source commit.

## Known Device Limitation

On the tested `CS7501` combination with main firmware
`bd3rCS_RFSBTWD45hb_G616p2` and hardware `GD32F350`, the following Full Read
items can return no response or an unparseable response:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

These results are classified as device/firmware limitations, not SDK API
defects. The SDK session remains usable and a following normal command
succeeds. The scope must not be generalized to other CS7501 firmware or
hardware combinations.
