# Release 0.1.1

This release closes the first public mobile SDK packaging pass. It focuses on
query API cleanup, bilingual sample-app polish, Android compatibility warning
cleanup, and release documentation.

## Added

- Added missing Bluetooth SSI master-command documentation.
- Added Bluetooth query controls, decoder module switching, immediate scan,
  `%KB#SP0`, and the `$BUZZ#B0` through `$BUZZ#BJ` buzzer command set across
  `core`, `api-c`, Android, and Swift.
- Added Bluetooth firmware version, Bluetooth name, Bluetooth address, and
  decoder module fields to `ScannerInfo`.
- Added active read APIs:
  - C API: `nsdk_refresh_info` / `nsdk_get_battery_info`
  - Kotlin: `refreshInfo()` / `getBatteryInfo()`
  - Swift: `refreshInfo()` / `getBatteryInfo()`
- Added cached info APIs:
  - C API: `nsdk_get_cached_info`
  - Kotlin: `getCachedInfo()`
  - Swift: `getCachedInfo()`

## Changed

- Changed the release license to `Netum Scanner SDK License`: the SDK is free
  for commercial application integration, closed-source, and must not be
  redistributed as a standalone SDK, component library, or source package.
- Reduced initialization queries to the minimum required path and removed the
  full public configuration-query API.
- Updated sample-app state displays to prefer typed models and session cache.
- Updated the C API smoke sample to demonstrate `refresh + cached`.
- Removed legacy `getInfo/getConfig` APIs.
- Updated Android and iPhone sample documentation for two-page layout, bilingual
  switching, and log export behavior.

## Fixed

- Fixed reversed parsing and switch semantics for `$MOTO#0 / $MOTO#1`.
- Fixed timeout handling while waiting for the first record in ACK-only streaming
  responses.
- Fixed ACK-only master commands not updating the session configuration cache.
- Fixed inconsistent cache updates between text-command and enum-command entry
  points.
- Fixed the Android sample `ScannerInfo` JNI constructor signature mismatch.
- Fixed frozen quick-action and command-group text after Android / iOS sample
  language switching.
- Fixed Android sample layout not avoiding system status and navigation bars.
- Suppressed Android `compileSdk 35` guidance noise and old BLE API warnings.

## Documentation

- Updated `README.md`.
- Updated Android sample documentation.
- Updated iOS platform documentation.
- Updated device regression checklist documentation.

## Validation

- `ctest` passed.
- `./gradlew test` passed.
- `swift test` passed.
