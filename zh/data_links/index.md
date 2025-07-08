# 数据链

数据链是用于将机体遥测数据(位置、速度、电池状态等)从飞控传输到地面控制站(及某些遥控系统)，以及将地面站命令传输到机体的无线电通道。这些链接通常使用与手动遥控机体所用无线电不同的无线电设备创建。PX4 使用 [MAVLink](https://mavlink.io/en/) 协议通过无线电通道进行串行数据通信。

本节提供可使用的各种无线电系统的相关信息，以及如何配置它们与 PX4 配合使用。

- [MAVLink 遥测 (OSD/GCS)](../peripherals/mavlink_peripherals.md) — 配置飞控输出用于遥测
- [遥测无线电](../telemetry/index.md) — 数据链常用协议和无线电系统
- [FrSky 遥测](../peripherals/frsky_telemetry.md) — 在 (FRSky) 遥控接收器上使用遥测
- [TBS Crossfire (CRSF) 遥测](../telemetry/crsf_telemetry.md) — 在 (TBS Crossfire) 遥控接收器上使用遥测
- [卫星通信 (Iridium/RockBlock)](../advanced_features/satcom_roadblock.md) — 通过卫星的高延迟通信

## 参见

- [安全配置 > 数据链丢失故障安全](../config/safety.md#data-link-loss-failsafe)