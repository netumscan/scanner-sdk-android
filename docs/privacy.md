# Mobile SDK Privacy

Scanner SDK communicates with nearby scanner devices. The SDK does not track
users and does not send scan data or device data off the device.

The official demo applications:

- keep scan text only in the current UI state;
- do not persist or export scan plaintext or raw hex;
- log only scan type, charset, and byte lengths;
- redact full device identifiers and serial numbers;
- do not upload logs;
- export diagnostics only after an explicit local user action.

The SwiftPM SDK privacy manifest declares no tracking and no collected data.
The iOS demo declares its local `UserDefaults` access with required-reason
category `CA92.1`.

The host application remains responsible for its own storage, analytics,
network transmission, retention, consent, and privacy disclosures.
