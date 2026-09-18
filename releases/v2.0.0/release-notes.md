# Scanner SDK Mobile Release 2.0.0

## Breaking Change and migration

Android and iOS BLE discovery now returns every device with a valid ID reported by the platform, including unnamed and unrelated devices and advertisements without service UUIDs. There is no legacy filtering mode. Apps must choose their own candidates.

`selectedModelKey` no longer filters or infers a model during BLE discovery. Discovered models remain unknown; model selection still applies to connection requests. Non-BLE discovery and device capability profiles are unchanged.

The new independent C v2 discovery callback supplies advertisement names, full service UUIDs, manufacturer company IDs and payloads, service data, and nullable connectability. Company IDs are not USB vendor IDs. Kotlin and Swift copy borrowed callback data before asynchronous delivery. Missing fields retain previous observations; exact duplicates are suppressed, and the latest RSSI is reported.

Product version is `2.0.0`. Native C ABI remains `1.0.0` (`0x010000`), with all published v1 layouts preserved. Old C discovery callbacks also receive all devices. Rebuild callers and use wrappers and native binaries from this version together; an older runtime does not contain the v2 symbol required by the new mobile wrappers.

See the Android and iOS integration guides for App-side candidate examples. To roll back the discovery behavior, use an earlier SDK version; there is no filtering switch.

## Demo and connection behavior

Both demos display all discovered devices without a count limit. Unnamed devices use a display placeholder while the raw name remains empty. Case-insensitive `Scanner` / `Barcode` name hints only affect candidate ordering, followed by descending RSSI and stable name/ID ordering. They do not confirm a device model. Devices explicitly marked nonconnectable remain visible with connection disabled.

The SDK does not automatically connect to or probe strangers. After the user chooses a device, existing GATT and session validation still applies. Apple adds Nordic UART support. Scan events and command responses remain independent; trigger-scan commands require a matching binary ACK. Receiving a barcode does not acknowledge a command, and devices returning only text ACKs can time out.

Platform permissions, hardware and OS policies affect visible devices and available fields; identical Android and iOS discovery results are not guaranteed. Demo build is `15`. This release does not publish APK, AAB, IPA, TestFlight, .NET/NuGet or Linux preview packages.

## RW-185 observations

The user confirmed discovery, connection and scanning on both Android and iPhone during implementation testing. This does not establish support for every advertisement or hardware variant.

For the previously tested RW185_CBTDE52h_G319 firmware over Android BLE, keep each scan within 1024 bytes including the final 0x0D (at most 1023 payload bytes). Payloads of 500 and 1000 bytes passed exact comparison; 1024, 1200 and 1500 byte payloads showed missing or corrupted data. The 1023 byte boundary remains unverified. This is usage guidance for the tested device, not a new SDK buffer limit or a guarantee for other models.

An unset MasterReplaceRule returns `nul` normally. The tested Demo still reports no usable setting value for this state. Unsolicited `+HIDDLY=2` text can still enter the scan stream when no matching query is pending.

## Known Device Limitation

On the tested `CS7501` combination with main firmware `bd3rCS_RFSBTWD45hb_G616p2` and hardware `GD32F350`, the following items remain classified as a device/firmware limitation:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

The scope must not be generalized to other firmware, hardware, or scanner models.
