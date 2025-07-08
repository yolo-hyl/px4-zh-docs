# Pixracer 线路快速入门

:::warning
PX4 不制造此（或任何）自动驾驶仪。  
联系 [制造商](https://store.mrobotics.io/) 获取硬件支持或合规问题。  
:::

:::warning
正在建设中  
:::

本快速入门指南展示了如何为 [Pixracer](../flight_controller/pixracer.md) 飞行控制器供电并连接其最重要的外围设备。

<img src="../../assets/flight_controller/pixracer/pixracer_hero_grey.jpg" width="300px" title="pixracer + 8266 grey" />

## 线路指南/组装

![Grau pixracer 双联](../../assets/flight_controller/pixracer/grau_pixracer_double.jpg)

### 主要设置

![Grau 设置 pixracer 顶部](../../assets/flight_controller/pixracer/grau_setup_pixracer_top.jpg)

![Grau 设置 pixracer 底部](../../assets/flight_controller/pixracer/grau_setup_pixracer_bottom.jpg)

### 遥控器/远程控制

如果您想 _手动_ 控制您的机体（PX4 在自主飞行模式下不需要无线电系统），则需要远程控制（RC）无线电系统。

您需要 [选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md) 并进行 _绑定_ 以使它们能够通信（请阅读随附的特定发射机/接收机的说明书）。

以下说明展示了如何连接不同类型的接收机：

- FrSky 接收机通过显示的端口连接，并可通过提供的 I/O 连接器连接。

  ![Grau B Pixracer FrSkyS.Port 连接](../../assets/flight_controller/pixracer/grau_b_pixracer_frskys.port_connection.jpg)

  ![Pixracer FrSkyS.Port 连接](../../assets/flight_controller/pixracer/pixracer_FrSkyTelemetry.jpg)

- PPM-SUM 和 S.BUS 接收机连接到 **RCIN** 端口。

  ![无线电连接](../../assets/flight_controller/pixracer/grau_setup_pixracer_radio.jpg)

- 具有 _每个通道单独导线_ 的 PPM 和 PWM 接收机必须通过 _PPM 编码器_ [如这个](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html) 连接到 **RCIN** 端口（PPM-Sum 接收机使用单根信号线传输所有通道）。

### 电源模块 (ACSP4)

![Grau ACSP4 2 roh](../../assets/flight_controller/pixracer/grau_acsp4_2_roh.jpg)

### 外部遥测

Pixracer 内置 WiFi，但也支持通过连接到 `TELEM1` 或 `TELEM2` 端口的外部 Wi-Fi 或无线电遥测模块进行遥测。  
这在下图的线路图中展示。

![Pixracer 外部遥测选项](../../assets/flight_controller/pixracer/pixracer_top_telemetry.jpg)

::: info
`TELEM2` 端口必须使用 [MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG) 参数配置为第二个 MAVLink 实例。  
更多信息请参见 [MAVLink 外设 > MAVLink 实例](../peripherals/mavlink_peripherals.md#mavlink-instances)（以及 [串口配置](../peripherals/serial_configuration.md)）。  
:::