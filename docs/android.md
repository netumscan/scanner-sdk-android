# Android Integration Guide

## Installation

For the public release, integrate the SDK through the Maven AAR:

```kotlin
repositories {
    mavenCentral()
}

dependencies {
    implementation("com.netumscan:scanner-sdk-android:0.1.3")
}
```

The public Android distribution repository is:

- https://github.com/netumscan/scanner-sdk-android

If a release overrides `POM_GROUP_ID` or `POM_ARTIFACT_ID`, use the Maven
coordinate recorded in that release note and generated POM.

Current Android SDK build requirements:

- `minSdk = 26`
- `compileSdk = 35`
- Java / Kotlin JVM target: `17`
- NDK: `26.1.10909125`
- CMake: `3.22.1`
- The AAR includes native libraries built by Gradle and CMake. Confirm the ABI
  list before shipping to a target device fleet.

## Recommended Flow

1. Select the scanner model being tested.
2. Initialize the SDK.
3. Request Bluetooth and nearby-device permissions.
4. Call `startDiscovery(..., selectedModelId = ...)`.
5. Observe `discoveryEvents` and `discoveryFailures`.
6. Connect to the target device.
7. After the session reaches `READY`, read `getResolvedModelId()` and
   `getDeviceCapabilitySummary()`.
8. Observe `scanEvents` and `failures`.
9. Read or update device configuration only after the session is ready.

## Minimum Permission Setup

The SDK AAR declares Bluetooth permissions, but the host application must still
handle runtime permissions and system Bluetooth state. Keep these permissions in
the host manifest so reviews and privacy disclosures are explicit:

```xml
<uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
```

Only add `android:usesPermissionFlags="neverForLocation"` to
`BLUETOOTH_SCAN` after validating that your app does not infer location from BLE
scans and the target ROMs still discover devices correctly.

Runtime permission example:

```kotlin
val permissions = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
    arrayOf(
        Manifest.permission.BLUETOOTH_SCAN,
        Manifest.permission.BLUETOOTH_CONNECT,
    )
} else {
    arrayOf(Manifest.permission.ACCESS_FINE_LOCATION)
}
```

Before calling `startDiscovery(...)`, confirm:

- runtime permissions are granted;
- system Bluetooth is enabled;
- location services are enabled on Android 11 and below;
- on Android 12+, check ROM-specific Bluetooth/location behavior if discovery
  returns no callbacks.

## Key Behavior

- Android BLE uses the `scanner_master` profile by default.
- Pass the selected scanner model into discovery so candidates can be filtered
  by known model fingerprints.
- Discovery does not report BLE devices with an empty Bluetooth name.
- If advertisements cannot identify the model reliably, the model selected by
  the integrating app remains the business source of truth.
- `connectReady(..., selectedModelId = ...)` stores the selected model in the
  session; without an explicit selection it falls back to `device.modelId`.
- After connection, discovery and resolved-model data should be treated as
  diagnostics, not as a replacement for the app-selected model.

## API Starting Points

`ScannerSdk`:

- `startDiscovery(transports, selectedModelId)`
- `connectReady(...)`
- `discoveryEvents`
- `discoveryFailures`
- `sessionFailures`
- `getDeviceModelProfile(modelId)`

`ScannerSession`:

- `state`
- `scanEvents`
- `failures`
- `getResolvedModelId()`
- `setPreferredModel(...)`
- `getDeviceCapabilitySummary()`
- `canExecuteMasterCommand(...)`
- `canExecuteModuleCommand(...)`
- `canExecuteDefaultModuleCommandProbe(...)`

For configuration UI or diagnostic panels, use the public runtime metadata:

- `getNt212xParameterDefinitions()`
- `getNt280hParameterDefinitions()`
- `getSe4750ParameterDefinitions()`
- `getNtc06hSettingDefinitions()`

## Error Handling

When a direct call fails, handle `ScannerException` first and log at least the
operation and SDK error code:

```kotlin
try {
    val session = ScannerSdk.connectReady(
        deviceId = device.deviceId,
        transportType = device.transportType,
        selectedModelId = selectedModelId,
    )
    session.initializeSession()
} catch (error: ScannerException) {
    Log.e("ScannerSdk", "operation=${error.operation} code=${error.errorCode}", error)
}
```

`discoveryFailures` and `sessionFailures` are asynchronous failure streams. For
diagnostics, record `code`, `message`, `platformErrorCode`, and BLE issue fields.
Do not rely only on `Throwable.message`.

## Lifecycle Guidance

- Initialize with `ScannerSdk.initialize(context.applicationContext)` from the
  application or first scanner screen.
- Start discovery when the page is visible; stop discovery when the page is not
  visible or before connecting.
- Keep one active business session unless multi-device behavior and command
  serialization are explicitly designed and tested.
- Stop discovery when the app enters the background. Keeping BLE connections in
  the background requires separate app-level validation.
- When Bluetooth is disabled, permissions are revoked, the scanner powers off,
  or the connection drops, clear UI state through `sessionFailures` / `failures`
  and allow rediscovery.
- Serialize commands. Do not send refresh, battery, and module parameter writes
  to the same session concurrently.
- Do not log full scan content by default. Debug logging must be explicitly
  enabled by the user and redacted before export.

Prefer `ScanEvent.textBytes` / `rawBytes` as the source data, then decode with
the business-selected `ScanTextCharset`. Do not treat `event.text` as the only
source of truth.
