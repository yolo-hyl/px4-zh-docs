# 遥测无线电/调制解调器集成

遥测无线电（可选）可用于在_QGroundControl_等地面控制站与运行PX4的机体之间提供无线MAVLink连接。本节包含关于支持的无线电的高级用法以及将新遥测系统集成到PX4中的主题。

:::tip
[Peripheral Hardware > Telemetry Radios](../telemetry/index.md) 包含PX4已支持的遥测无线电系统信息。这包括使用_SiK Radio_固件和_3DR WiFi Telemetry Radios_的无线电。
:::

## 集成遥测系统

PX4通过Pixhawk系列飞控的遥测端口启用基于MAVLink的遥测功能。只要遥测无线电支持MAVLink并提供具有兼容电压电平/连接器的UART接口，则无需进一步集成。

使用其他协议通信的遥测系统将需要更广泛的集成，可能涵盖软件（例如设备驱动程序）和硬件（连接器等）。尽管已经为特定情况（例如[FrSky Telemetry](../peripherals/frsky_telemetry.md) 可通过FrSky接收机向遥控器发送机体状态）实现了集成，但提供通用建议较为困难。我们建议您首先[与开发团队讨论](../contribute/support.md)。