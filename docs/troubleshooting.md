# Mobile Troubleshooting

## No devices discovered

- Confirm Bluetooth is enabled.
- Confirm runtime permissions are granted.
- On Android 11 and below, confirm location services are enabled.
- Stop any existing discovery before starting a new discovery.
- Verify the selected model and transport match the scanner.

## Connect does not become Ready

- Stop discovery before connecting.
- Observe structured SDK and session failures.
- Confirm the scanner is not connected to another host.
- Disconnect the old session, power-cycle the scanner if necessary, then
  rediscover.

## Text is garbled

- Inspect `charset`, `textBytes`, and `rawBytes`.
- Configure the local scan charset explicitly. Use GBK when the scanner emits
  GBK bytes.
- Do not treat the convenience `text` field as the only source of truth.

## A setting read has no response

- Record the setting key, request metadata, device model, hardware, main
  firmware, and Bluetooth firmware.
- Confirm the next normal command succeeds and the session remains usable.
- Check the known-limitations document for an exact firmware/hardware match.
- Do not assume the limitation applies to another firmware combination.

## Reporting an issue

Include SDK version, SDK commit, demo version/build, platform version, scanner
model/firmware, transport, operation, SDK error code, and sanitized reproduction
steps. Remove scan content, raw hex, serial numbers, and full device IDs.
