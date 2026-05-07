# Platform Permissions and System Configuration

BLE, USB HID, and USB Serial require platform permissions, enabled hardware, and
system configuration before SDK calls can succeed. The SDK should return clear
failures when these requirements are not met.

## Android

BLE GATT requirements:

- Android 12+: `BLUETOOTH_SCAN` and `BLUETOOTH_CONNECT`.
- Android 11 and below: location permission and location services are commonly
  required for BLE scanning.
- Runtime permissions must be granted before discovery.
- System Bluetooth must be enabled.

Recommended manifest:

```xml
<uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
```

`BLUETOOTH_SCAN` with `neverForLocation` is not the default recommendation. Use
it only when the host app does not infer location from BLE scans and target ROMs
have been verified:

```xml
<uses-permission
    android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation" />
```

If devices disappear after adding this flag, compare with the sample app and a
BLE scanner such as `nRF Connect`, then decide whether to remove the flag or add
location permission guidance.

Recommended order:

1. Check Bluetooth hardware availability.
2. Check whether Bluetooth is enabled.
3. Request runtime permissions.
4. Check location services on Android 11 and below.
5. Call `startDiscovery()`.

## iOS

BLE GATT requirements:

- Add a Bluetooth usage description to `Info.plist`.
- CoreBluetooth prompts for authorization when first used.
- If the user denies Bluetooth permission, the SDK can only report a failure.
- Background scan/connect is not a default capability. It must be configured and
  validated by the host app.

Recommended `Info.plist`:

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string>Connect to scanner devices and receive scan data.</string>
```

Older project templates may also keep:

```xml
<key>NSBluetoothPeripheralUsageDescription</key>
<string>Connect to scanner devices and receive scan data.</string>
```

Integration documentation should state whether background mode is supported, how
the app guides users to enable Bluetooth permission, and what the UI does after a
discovery timeout.

## macOS

BLE GATT requirements:

- The app needs Bluetooth permission.
- Sandboxed apps need the appropriate entitlement.
- Command-line samples and signed production apps may behave differently and
  should not be treated as interchangeable validation.

When macOS support is marked as verified, specify whether it was verified with a
command-line sample, a development app, or a signed production app.

## Windows

BLE GATT requirements:

- Windows 10 19041+.
- Bluetooth hardware and system Bluetooth switch.
- The WinRT BLE watcher can fail because of permissions, policy, hardware, or
  scan conflicts.

USB HID requirements:

- The scanner must expose a HID Report channel.
- The device must not be held by another process.
- Report id, framing mode, and read/write report lengths must match the device
  profile.

USB Serial requirements:

- The COM port or device id must be correct.
- The port must not be held by another process.
- Baud rate, parity, data bits, and stop bits must be documented for the target
  device mode.

## Linux

Linux support is still planned. Before marking it as verified, document:

- BlueZ version requirements;
- D-Bus permission requirements;
- udev rules;
- USB HID access permissions;
- serial user group requirements such as `dialout`;
- differences between systemd services and desktop app scenarios.

## Failure Messages

Permission and system failures should include:

- platform;
- transport;
- likely cause;
- user action;
- whether retry is possible.

Example:

```text
Windows BLE discovery failed: Bluetooth is disabled or unavailable.
Turn on Bluetooth and retry discovery.
```
