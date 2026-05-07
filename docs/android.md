# Android 接入指南

## 安装

正式发布态推荐通过 Maven AAR 接入：

```kotlin
repositories {
    mavenCentral()
}

dependencies {
    implementation("com.netumscan:scanner-sdk-android:0.1.1")
}
```

Android SDK 的公开分发仓库是 `https://github.com/netumscan/scanner-sdk-android`。如果发版时覆盖了 `POM_GROUP_ID` / `POM_ARTIFACT_ID`，以该版本 release note 和生成的 POM 为准。

当前 Android SDK 约束：

- `minSdk = 26`
- `compileSdk = 35`
- Java / Kotlin JVM target：`17`
- NDK：`26.1.10909125`
- CMake：`3.22.1`
- 当前 AAR 由 Gradle/CMake 构建 native library；发版前必须确认 AAR 内 ABI 列表符合客户目标设备要求

## 推荐接入顺序

1. 选择本轮测试目标型号
2. 初始化 SDK
3. 申请蓝牙与附近设备权限
4. 调用 `startDiscovery(..., selectedModelId = ...)`
5. 监听 `discoveryEvents` / `discoveryFailures`
6. 连接目标设备
7. 会话进入 `READY` 后读取 `getResolvedModelId()` 和 `getDeviceCapabilitySummary()`
8. 监听 `scanEvents` / `failures`
9. 再读取或更新配置

## 权限最小配置

SDK AAR 会声明蓝牙权限，但宿主 App 仍必须处理运行时授权和系统开关。建议宿主 Manifest 显式保留这些权限，便于代码审查和隐私说明：

```xml
<uses-permission android:name="android.permission.BLUETOOTH" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" android:maxSdkVersion="30" />
<uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" android:maxSdkVersion="30" />
```

如果宿主明确不会从 BLE 扫描推导位置，并且目标 ROM 验证通过，可以评估给 `BLUETOOTH_SCAN` 增加 `android:usesPermissionFlags="neverForLocation"`。不要未经验证就加：部分 ROM 仍会把 BLE scan 和位置权限/定位开关绑在一起。

运行时检查建议：

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

在调用 `startDiscovery(...)` 前必须确认：

- 权限已授予
- 系统蓝牙已开启
- Android 11 及以下定位服务已开启
- Android 12+ 若 discovery 无回调，按 ROM 兼容策略检查位置权限和定位服务

## 关键口径

- Android BLE 默认走 `scanner_master`
- discovery 入口应传测试目标型号，先按型号对应硬件指纹过滤候选设备
- SDK discovery 不再向上输出蓝牙名称为空的 BLE 设备
- 如果广播内容仍无法稳定识别型号，接入方选择的型号就是最终业务口径，不要再让 discovery 解析结果反向覆盖
- `connectReady(..., selectedModelId = ...)` 会把接入方选择的型号写入 session；没有显式选择时才退回 `device.modelId`
- 连接成功后，SDK 应按接入方明确选择的型号设置会话画像；discovery / resolved model 只保留为诊断信息

## 建议优先关注的 API

### `ScannerSdk`

- `startDiscovery(transports, selectedModelId)`
- `connectReady(...)`
- `discoveryEvents`
- `discoveryFailures`
- `sessionFailures`
- `getDeviceModelProfile(modelId)`

### `ScannerSession`

- `state`
- `scanEvents`
- `failures`
- `getResolvedModelId()`
- `setPreferredModel(...)`
- `getDeviceCapabilitySummary()`
- `canExecuteMasterCommand(...)`
- `canExecuteModuleCommand(...)`
- `canExecuteDefaultModuleCommandProbe(...)`

如果你要做配置 UI 或调试面板，优先直接用 Kotlin 层已经公开的 runtime metadata：

- `getNt212xParameterDefinitions()`
- `getNt280hParameterDefinitions()`
- `getSe4750ParameterDefinitions()`
- `getNtc06hSettingDefinitions()`

`Quick Actions` 的跨平台语义约束见：

- [../demo/demo-quick-actions.md](../demo/demo-quick-actions.md)

发现指纹维护表见：

- [../../architecture/device-specs/device-discovery-fingerprint-matrix.md](../../architecture/device-specs/device-discovery-fingerprint-matrix.md)

完整 Kotlin API 说明见：

- [../../api/kotlin-api.md](../../api/kotlin-api.md)

## 错误处理

直接调用失败时优先识别 `ScannerException`，日志至少保留 `operation` 和 `errorCode`：

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

`discoveryFailures` / `sessionFailures` 是异步 failure 事件；排障时同时记录 `code`、`message`、`platformErrorCode` 和 BLE issue 字段。不要只把 `Throwable.message` 当作唯一错误依据。

## 生命周期与重连建议

业务 App 不要把 `ScannerSession` 塞进短生命周期 View。推荐由 `ViewModel` 或业务级 manager 持有 session，并把 `state`、`scanEvents`、`failures` 转成 UI 状态。

建议规则：

- `Application` 或首个扫描页面启动时调用 `ScannerSdk.initialize(context.applicationContext)`。
- 页面可见时启动 discovery，页面不可见或准备连接前调用 `stopDiscovery()`。
- 连接成功后只保留一个当前业务 session；如果要支持多设备，必须先定义 UI 和命令串行策略，再做真机回归。
- App 进入后台时至少停止 discovery；是否保持 BLE 连接由业务决定，但不能宣称后台稳定连接，除非完成后台模式验证。
- 用户关闭蓝牙、撤销权限、设备断电或超距断连时，以 `sessionFailures` / `failures` 为准清理 UI，并允许用户重新 discovery。
- 命令执行必须串行化。不要让 `Refresh Info`、`Get Battery`、模组写参数同时打到同一个 session。
- 日志默认不要输出完整扫码内容；调试模式需要用户显式开启，并在导出前脱敏。

扫码数据建议优先使用 `ScanEvent.textBytes` / `rawBytes`，再按业务配置的 `ScanTextCharset` 解码。不要把 `event.text` 当唯一真源。
