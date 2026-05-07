# 平台权限与系统配置

> 文档类型：接入指南
> 目标读者：Android / iOS / macOS / Windows / Linux 接入方
> 关联模块：`wrappers/`、`adapters/`
> 更新条件：平台最低版本、权限声明、系统配置、传输实现变化时必须更新

## 结论

BLE、USB HID、USB Serial 都不是“只调 API 就能跑”的能力。接入方必须先满足系统权限、硬件开关和平台配置，否则 SDK 应返回明确 failure，而不是静默失败。

## Android

BLE GATT 需要关注：

- Android 12+：`BLUETOOTH_SCAN`、`BLUETOOTH_CONNECT`
- Android 11 及以下：通常需要位置权限和系统定位开关
- App 运行时权限必须在 discovery 前完成
- 蓝牙开关必须开启

推荐 Manifest：

```xml
<uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
```

`BLUETOOTH_SCAN` 的 `neverForLocation` 不是默认建议。只有宿主 App 明确不会用 BLE scan 推导位置，并且目标 ROM 验证通过，才可以加：

```xml
<uses-permission
    android:name="android.permission.BLUETOOTH_SCAN"
    android:usesPermissionFlags="neverForLocation" />
```

如果加了这个 flag 后出现扫描不到设备，先用 demo 和 `nRF Connect` 对照验证，再决定是否移除该 flag 或补位置权限说明。

建议接入顺序：

1. 检查蓝牙硬件是否存在。
2. 检查蓝牙是否开启。
3. 请求运行时权限。
4. Android 11 及以下检查定位服务。
5. 再调用 `startDiscovery()`。

文档或 Demo 如果声明 Android BLE 可用，必须说明目标 Android 版本和权限处理方式。

## iOS

BLE GATT 需要关注：

- `Info.plist` 中声明蓝牙使用说明。
- App 首次使用 CoreBluetooth 时会触发系统授权。
- 用户拒绝蓝牙权限后，SDK 只能给出 failure，不能自行恢复。
- 后台扫描/连接不是默认能力，除非宿主 App 明确配置并验证。

推荐 `Info.plist`：

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string>用于连接扫描枪设备并接收扫码数据</string>
```

如需兼容旧工程模板，可以同时保留：

```xml
<key>NSBluetoothPeripheralUsageDescription</key>
<string>用于连接扫描枪设备并接收扫码数据</string>
```

必须在接入文档中说明：

- 是否支持后台模式。
- App 如何提示用户打开蓝牙权限。
- discovery 超时后的 UI 行为。

## macOS

BLE GATT 需要关注：

- App 需要蓝牙权限。
- 沙盒 App 需要相应 entitlement。
- 命令行 demo 和正式 App 的权限表现可能不同，不能互相替代验证。

macOS 文档中写“已验证”时，必须说明是命令行 demo、开发态 App，还是正式签名 App。

## Windows

BLE GATT 需要关注：

- Windows 10 19041+。
- 蓝牙硬件和系统蓝牙开关。
- WinRT BLE watcher 可能因为权限、策略、硬件或扫描冲突失败。

USB HID 需要关注：

- 设备是否暴露 HID Report 通道。
- 设备是否被其他程序占用。
- report id、framing mode、读写 report 长度必须和设备画像一致。

USB Serial 需要关注：

- 串口名或设备 ID 是否正确。
- 端口是否被其他程序占用。
- 波特率、校验位、数据位、停止位必须记录在文档中。

Windows 文档中写“可用”时，必须区分 BLE、USB HID、USB Serial，不能只写“Windows 支持”。

## Linux

Linux 当前仍是规划状态。后续落地时必须补齐：

- BlueZ 版本要求。
- D-Bus 权限要求。
- udev 规则。
- USB HID 访问权限。
- 串口用户组要求，例如 `dialout`。
- systemd service 或桌面 App 场景下的差异。

没有这些内容前，Linux 不能标为 `Verified`。

## 权限失败文案规则

错误提示必须同时包含：

- 失败平台。
- 失败传输。
- 可能原因。
- 用户可执行动作。
- 是否可重试。

示例：

```text
Windows BLE discovery failed: Bluetooth is disabled or unavailable.
Turn on Bluetooth and retry discovery.
```

## 关联文档

- [android.md](android.md)
- [ios.md](ios.md)
- [windows.md](windows.md)
- [linux.md](linux.md)
- [../troubleshooting.md](../troubleshooting.md)
