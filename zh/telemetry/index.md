# 遥测无线电/调制解调器

遥测无线电（可选）可用于在QGroundControl等地面控制站与运行PX4的机体之间建立无线MAVLink连接。这使得可以在飞行过程中调整参数、实时检查遥测数据、动态修改任务等。

PX4支持多种类型的遥测无线电：

- [SiK Radio](../telemetry/sik_radio.md) 基于固件（更通用地说，任何带有UART接口的无线电都应兼容）。
  - [HolyBro SiK Telemetry Radio](../telemetry/holybro_sik_radio.md)
  - [RFD900 Telemetry Radio](../telemetry/rfd900_telemetry.md)
  - [ThunderFly TFSIK01 Telemetry Radio](../telemetry/tfsik_telemetry.md)
  - <del>_HKPilot Telemetry Radio_</del> (已停产)
  - <del>_3DR Telemetry Radio_</del> (已停产)
- [Telemetry Wifi](../telemetry/telemetry_wifi.md)
- [J.Fi Wireless Telemetry Module](../telemetry/jfi_telemetry.md)
- [Microhard Serial Telemetry Radio](../telemetry/microhard_serial.md)
  - [ARK Electron Microhard Serial Telemetry Radio](../telemetry/ark_microhard_serial.md)
  - [Holybro Microhard P900 Telemetry Radio](../telemetry/holybro_microhard_p900_radio.md)
- CUAV Serial Telemetry Radio
  - [CUAV P8 Telemetry Radio](../telemetry/cuav_p8_radio.md)
- XBee Serial Telemetry Radio
  - <del>[HolyBro XBP9X Telemetry Radio](../telemetry/holybro_xbp9x_radio.md)</del> (已停产)

PX4与[SiK Radio](../telemetry/sik_radio.md)在协议上兼容，通常即插即用（尽管可能需要更换/使用合适的连接器）。

WiFi遥测通常具有较短传输距离、较高数据速率，且更容易支持FPV/视频传输。WiFi无线电的优势之一是只需为机体购买一个无线电单元（假设地面站已具备WiFi功能）。

::: info
PX4不支持将LTE USB模块直接连接到飞控（并通过互联网传输MAVLink流量）。
但可以将LTE模块连接到伴飞计算机，并使用其路由飞控的MAVLink流量。
更多信息请参见：[伴飞计算机外设 > 数据通信](../companion_computer/companion_computer_peripherals.md#data-telephony-lte)。
:::

## 允许的频率带

不同大陆、地区、国家甚至州对无人机使用的无线电频段有不同规定。应选择符合计划使用区域法规的遥测无线电频率范围。

低功耗[SiK无线电](../telemetry/sik_radio.md)，如[HolyBro遥测无线电](../telemetry/holybro_sik_radio.md)，通常提供915 MHz和433 MHz版本。虽然应核查所在国家/州的适用法规，但大致而言，915 MHz可用于美国，433 MHz可用于欧盟、非洲、大洋洲及大部分亚洲地区。