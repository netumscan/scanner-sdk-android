# Android API Starting Points

`ScannerSdk` provides:

- read-only `version`
- initialization and shutdown
- discovery events and discovery failures
- connection and session failure streams
- model profiles and capability metadata

`ScannerSession` provides:

- state and scan-event streams
- device information, battery, and resolved model
- capability enumeration
- capability read, write, and action operations
- disconnect

Catch `ScannerException` and retain its operation and SDK error code. For
asynchronous failures, observe the SDK and session failure streams instead of
relying only on thrown exception messages.

Native loading and ABI compatibility are enforced automatically before SDK
initialization. See [Android native compatibility](android.md#native-compatibility)
for supported CPU ABIs and load-versus-version troubleshooting.

## Unfiltered BLE discovery

BLE discovery returns all platform observations with a valid device ID, including unnamed and unrelated devices. There is no legacy filtering switch. `selectedModelKey` is retained but ignored for discovery; model and match reason remain empty. Non-BLE discovery is unchanged.

`DiscoveredDevice` adds defaulted `advertisementName`, `serviceUuids`, `manufacturerData`, `serviceData`, and nullable `connectable`. UUIDs are complete and lowercase. Manufacturer map keys are Bluetooth company IDs, not USB VIDs; payload excludes the two-byte company ID. Service Data bytes are unchanged. A missing field means unknown and does not clear cached data. Each scan clears the cache; first observations and changes (including weaker RSSI) are emitted, identical updates are suppressed. Platform coverage and available fields can differ.

Use a map keyed by `deviceId`. Show unnamed devices with a display placeholder, keep every device, and disable connect only when `connectable == false`. Candidates are an app hint: name or advertised name contains `Scanner` / `Barcode`, case-insensitively. Sort candidates first, then RSSI descending (unknown last), lowercase name and device ID. Never auto-connect or probe unfamiliar devices. User-initiated connection still validates GATT and runs the existing session flow.

The wrapper uses the independent C discovery v2 callback and copies borrowed data before asynchronous delivery. Ship it with the matching new native library and rebuild callers; old v1 C callbacks remain supported with their original fields and now receive all BLE devices too. Rollback requires reverting the version, not toggling a filter mode.

```kotlin
fun candidate(d: DiscoveredDevice) = listOf(d.name, d.advertisementName).any {
    it.contains("Scanner", true) || it.contains("Barcode", true)
}
val canConnect = device.connectable != false
val label = device.name.ifBlank { "Unnamed device" }
```
