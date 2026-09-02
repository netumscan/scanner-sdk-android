# Scanner SDK Mobile Release 1.1.0

## Highlights

- Adds `L6BL`, `DS5000`, `E740`, `W28`, `CS7505`, `U820`, `X-6800`,
  `RD-I8`, `CS8515`, and `RW-185` to the scanner model catalog.
- Maps the new models to the confirmed `NTC06H`, `NT212X`, `NT280H`, or
  `SE4750` module-family template from the V4.1 standard BOM.
- Exposes the new models through the existing Android Kotlin and Apple Swift
  model, capability-entry, and setting-code-entry APIs.

## Compatibility

- Product version: `1.1.0`.
- Native C ABI remains `1.0.0` (`0x010000`); frozen symbols and structure
  layouts are unchanged.
- Android Kotlin and Apple Swift public API baselines are unchanged, so no
  source migration is required from `1.0.0`.
- Use the wrapper, C headers, and native binary from the same `1.1.0` package.

## Model Status

All ten added models are `CodeOnly`. Model resolution, template mapping,
transport enumeration, and capability/setting-code catalog access are covered
by automated tests. This status does not claim device verification.

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
