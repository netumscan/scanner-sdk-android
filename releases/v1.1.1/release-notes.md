# Scanner SDK Mobile Release 1.1.1

## Highlights

- Android discovery starts without a system service UUID filter; scanner names receive priority in Demo device lists.
- Android adds Nordic UART GATT support and MTU negotiation before notification setup, with a timeout fallback.
- Fixes the Android Demo model dropdown being obscured by system navigation.
- Core recognizes RW185 version responses during version queries and includes long-scan reassembly coverage.
- Includes shared Core fixes for CS7501 response parsing and HID payload lengths.

## Compatibility

Product version is `1.1.1`. Native C ABI remains `1.0.0` (`0x010000`). Android Kotlin and Apple Swift public APIs are unchanged. Use wrappers and native binaries from the same product version. Nordic UART and MTU changes in this release apply to Android; no equivalent Apple adapter change is claimed. Model support statuses remain unchanged.

## RW-185 observations

For the tested RW185_CBTDE52h_G319 firmware over Android BLE, keep each scan within 1024 bytes including the final 0x0D (at most 1023 payload bytes). Payloads of 500 and 1000 bytes passed exact comparison; 1024, 1200 and 1500 byte payloads showed missing or corrupted data. The 1023 byte boundary remains unverified. This is usage guidance for the tested device, not a new SDK buffer limit or a guarantee for other models.

An unset MasterReplaceRule returns `nul` normally. The tested Demo still reports no usable setting value for this state. Unsolicited `+HIDDLY=2` text can still enter the scan stream when no matching query is pending.

## Known Device Limitation

On the tested `CS7501` combination with main firmware
`bd3rCS_RFSBTWD45hb_G616p2` and hardware `GD32F350`, the following items remain
classified as a device/firmware limitation:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

The scope must not be generalized to other firmware, hardware, or scanner
models.
