# Security and Privacy

Scan content can contain business-sensitive data. SDK logs, sample apps, test
logs, issue reports, and screenshots must not expose raw scan content by default.

## Logging

- Do not log full scan content by default.
- Only log scan content in a user-enabled debug mode.
- Redact issue reports, regression records, and screenshots before sharing.
- Do not log tokens, certificates, authorization headers, Bluetooth pairing
  information, or customer-private device identifiers.
- Failure logs should keep error code, platform, transport, state-machine step,
  and payload length instead of sensitive payloads.

## Permission Disclosure

Platform integration documentation must explain why permissions exist:

- BLE scan: discover scanner devices.
- BLE connect: establish a GATT connection.
- Location: required only by older Android BLE scan behavior; it does not mean
  the SDK needs business location data.
- USB HID / Serial: communicate with wired devices.

Customers should not have to infer why a permission is required.

## Release Artifacts

- Official release artifacts must come from reproducible scripts or CI.
- Release packages must include version, checksum, and generation source.
- Release packages must carry the `Netum Scanner SDK License`. The SDK is free
  for commercial application integration, closed-source, and must not be
  redistributed as a standalone SDK or component.
- Do not use local temporary build directories as official distribution entry
  points.
- Do not publish build outputs or documentation that contain local absolute
  paths.

## Dependencies

Before adding a dependency, document:

- why it is needed;
- whether the standard library or an existing dependency can replace it;
- whether the license is compatible;
- whether it enters official release artifacts;
- whether it affects mobile package size or desktop installation size.

## Vulnerability Handling

When recording a security issue, include:

- affected platform;
- affected version;
- trigger conditions;
- potentially affected data types;
- mitigation;
- whether a patch release is required.

Do not publish directly exploitable details in public documentation before the
issue is confirmed and fixed through a private channel.
