# Known Mobile Limitations

## CS7501 Full Read

On the tested combination below, seven setting reads can return no response or
an unparseable response:

- Model: `CS7501`
- Profile: `master-nt212x`
- Transport: `BLE_GATT`
- Main firmware: `bd3rCS_RFSBTWD45hb_G616p2`
- Hardware: `GD32F350`

Affected setting keys:

- `DisablePassiveTriggerScan`
- `HanXinInverseDecodeMode`
- `MasterReplaceRule`
- `Pdf417InverseDecodeMode`
- `QrCodeUtf8Bom`
- `SuppressDuplicateInDecodeCycle`
- `TransportMode`

This is a device/firmware limitation, not an SDK API defect. After each result,
the release testbench verifies that the session remains Ready and a normal
command succeeds. This statement does not apply automatically to other CS7501
firmware or hardware combinations.
