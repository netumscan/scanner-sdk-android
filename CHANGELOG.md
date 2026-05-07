# Release 0.1.1

本次版本主要是发版收口和 demo 质量修正，没有改架构方向，重点是把查询接口迁移、demo 双语、Android 兼容警告和发布文档一起收干净。

## Added

- 补齐蓝牙 SSI 文档缺失主控指令
- `core` / `api-c` / Android / Swift 同步新增蓝牙查询控制、解码模组切换、立即扫描、`%KB#SP0`、整套 `$BUZZ#B0` 到 `$BUZZ#BJ`
- `ScannerInfo` 新增蓝牙固件版本、蓝牙名称、蓝牙地址、解码模组
- `ScannerConfig` 曾补齐接口模式、键盘口速、BT HID 延时、当前字符集、当前接收设备、键盘布局、终端符；后续已降为内部兼容模型，不再作为公开查询主入口
- 新增主动读取接口：
  - C API: `nsdk_refresh_info` / `nsdk_get_battery_info`
  - Kotlin: `refreshInfo()` / `getBatteryInfo()`
  - Swift: `refreshInfo()` / `getBatteryInfo()`
- 新增缓存读取接口：
  - C API: `nsdk_get_cached_info`
  - Kotlin: `getCachedInfo()`
  - Swift: `getCachedInfo()`

## Changed

- 查询链路后续已收敛为最小初始化，不再保留全量配置查询 API
- demo 状态展示优先走 typed model 和 session cache
- C API smoke sample 改为示范 `refresh + cached`
- 清理 legacy `getInfo/getConfig` API
- Android / iPhone demo 文档补齐双页结构、双语切换和日志导出说明

## Fixed

- 修正 `$MOTO#0 / $MOTO#1` 开关语义和反向解析错误
- 修正 ACK-only 流式响应等待首条记录时的超时处理
- 修正 ACK-only 主控命令无法回填 session 配置缓存
- 修正文本文令入口和枚举命令入口缓存更新不一致
- 修正 Android demo `ScannerInfo` JNI 构造签名不匹配
- 修正 Android / iOS demo 双语切换后快捷操作、命令分组等文案被冻结
- 修正 Android demo 页面未避开系统状态栏 / 导航栏
- 压掉 Android `compileSdk 35` 提示和 BLE 旧 API warning 噪音

## Docs

- 更新 `README.md`
- 更新 `docs/guides/demo/android-demo.md`
- 更新 `docs/guides/platforms/ios.md`
- 更新 `docs/guides/testing/device-regression-checklist.md`

## Validation

- `ctest` 全通过
- `./gradlew test` 全通过
- `swift test` 全通过
