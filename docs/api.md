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
