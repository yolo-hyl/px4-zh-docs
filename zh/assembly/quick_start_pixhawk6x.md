# Holybro Pixhawk 6X 接线快速入门

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系 [manufacturer](https://holybro.com/)。
:::

本快速入门指南介绍了如何为 [Pixhawk 6X<sup>&reg;</sup>](../flight_controller/pixhawk6x.md) 飞行控制器供电，并连接其最重要的外设。

## 套件内容

:::: tabs

::: tab Pixhawk 6X 标准套装

![Pixhawk 6x 标准套装](../../assets/flight_controller/pixhawk6x/pixhawk6x_standard_set.jpg)

:::

::: tab Pixhawk 6X Mini 套装

![Pixhawk 6x mini 标准套装](../../assets/flight_controller/pixhawk6x/pixhawk6x_mini_set.jpg)

:::
::::

## 接线图概览

下图是示例接线图，展示了如何连接最重要的传感器和外设。

![Pixhawk 6x Wiring Overview](../../assets/flight_controller/pixhawk6x/pixhawk6x_wiring_diagram.png)

:::tip
有关可用端口的更多信息，请参见：[Pixhawk 6X > Connections](../flight_controller/pixhawk6x.md#connections)。
:::

## 安装和调整控制器方向

_Pixhawk 6X_ 可使用套件中包含的双面胶带安装在机架上。  
应尽可能将其安装在机体的重心附近，正面朝上且箭头指向机体前方。

<img src="../../assets/flight_controller/pixhawk6x/pixhawk6x_vehicle_front1.jpg" width="400px" title="Pixhawk6x" />

::: info
如果控制器无法按推荐/默认方向安装（例如由于空间限制），则需要根据实际安装方向配置飞控软件：[飞控方向设置](../config/flight_controller_orientation.md)。
:::

## GPS + 指南针 + 蜂鸣器 + 安全开关 + LED

_Pixhawk6X 标准套装_ 与 _Pixhawk6X 迷你套装_ 可搭配 M8N 或 M9N GPS（10针接口）出售，应将其连接到 **GPS1** 端口。  
这些 GNSS 模块集成了指南针、安全开关、蜂鸣器和 LED。

可单独购买的 [M8N 或 M9N GPS](https://holybro.com/collections/gps)（6针接口）可连接到 **GPS2** 端口。

GPS/指南针应 [安装在机体框架上](../assembly/mount_gps_compass.md)，尽可能远离其他电子设备，方向标记应朝向机体前方（将指南针与其他电子设备分离可减少干扰）。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_gps_front.jpg" width="200px" title="Pixhawk5x 标准套装" />

::: info
GPS 模块的集成安全开关默认启用（启用时，PX4 将不允许您启动机体）。  
要禁用安全开关，请按住安全开关 1 秒。  
再次按下安全开关可启用安全功能并解除机体启动（这在您因任何原因无法通过遥控器或地面站解除机体启动时非常有用）。
:::

## 电源

将Standard Set配套的_PM02D Power Module_(PM板)输出，使用6芯线缆连接到_PIXhawk 6X_的**POWER**接口。  
Pixhawk 6X上的PM02D和Power接口使用[2.00mm Pitch CLIK-Mate Wire-to-Board PCB Receptacle](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670) & [Housing](https://www.molex.com/molex/products/part-detail/crimp_housings/5024390600)的6路电路。

PM02D电源模块支持**2~6S**电池，板输入端应连接到您的LiPo电池。注意：PM板不会向**FMU PWM OUT**和**I/O PWM OUT**的+/-引脚供电。

如果使用固定翼或地面车辆，**FMU PWM-OUT**需要单独供电以驱动舵机（如方向舵、升降舵等）。可通过将**FMU PWM-OUT**的8针电源(+)轨道连接到电压调节器实现（例如带有BEC的电调、独立5V BEC或2S LiPo电池）。

::: info
电源轨道电压必须适合所使用的舵机！
:::

| 引脚与连接器 | 功能                                            |
| ------------ | ----------------------------------------------- |
| I/O PWM Out  | 在此处连接电机信号和GND线缆。                   |
| FMU PWM Out  | 在此处连接舵机信号、正极和GND线缆。             |

::: info
PX4固件中的**MAIN**输出映射到_PIXhawk 6X_的**I/O PWM OUT**端口，而**AUX输出**映射到_PIXhawk 6X_的**FMU PWM OUT**。  
例如，**MAIN1**映射到**I/O PWM OUT**的IO_CH1引脚，**AUX1**映射到**FMU PWM OUT**的FMU_CH1引脚。
:::

_PIXhawk 6X_电源接口的引脚分配如下所示。电源接口从PM02D电源模块接收I2C数字信号以获取电压和电流数据。VCC线路必须至少提供3A持续电流，并默认为5.2V。5V的较低电压仍可接受，但不推荐。

| 引脚      | 信号 | 电压  |
| --------- | ---- | ----- |
| 1(红色)   | VCC  | +5V   |
| 2(黑色)   | VCC  | +5V   |
| 3(黑色)   | SCL  | +3.3V |
| 4(黑色)   | SDA  | +3.3V |
| 5(黑色)   | GND  | GND   |
| 6(黑色)   | GND  | GND   |## 遥控

如果需要_手动_控制机体(PX4在自主飞行模式下不需要遥控系统)，则必须使用遥控(远程控制)无线电系统。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后对其进行_绑定_以实现通信(阅读随附发射机/接收机的具体说明)。

- Spektrum/DSM接收机连接到**DSM/SBUS RC**输入。
- PPM或SBUS接收机连接到**RC IN**输入端口。

使用_每个通道有独立导线_的PPM和PWM接收机，必须通过PPM编码器[如这个](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)连接到**RC IN**端口(PPM-Sum接收机使用单个信号线传输所有通道)。

关于选择遥控系统、接收机兼容性以及绑定发射机/接收机对的更多信息，请参见：[远程控制发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

[遥测无线电](../telemetry/index.md)可用于从地面站与机体进行通信和控制（例如，您可以指挥无人机飞往特定位置，或上传新的任务）。

机体侧无线电应连接到**TELEM1**端口（如连接到此端口，无需进一步配置）。
另一块无线电连接到您的地面站计算机或移动设备（通常通过USB连接）。

您还可以在[Holybro的官网](https://holybro.com/collections/telemetry-radios)购买相关无线电产品。

## SD卡（可选）

建议使用SD卡，因为它们需要用于[记录和分析飞行细节](../getting_started/flight_reporting.md)、运行任务以及使用UAVCAN-bus硬件。
将卡（包含在Pixhawk 6X中）按照下图所示插入_Pixhawk 6X_。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_sd_slot.jpg" width="420px" title="Pixhawk5x标准套装" />

:::tip
有关SD卡（可移动存储）的更多信息，请参见[基本概念 > SD卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机根据[机架参考手册](../airframes/airframe_reference.md)中针对机体指定的顺序，连接到**I/O PWM OUT** (**MAIN**) 和 **FMU PWM OUT** (**AUX**) 端口。

::: info
此参考手册列出了所有支持的空中和地面机架的输出端口到电机/舵机的映射关系（如果您的机架未在参考手册中列出，则使用对应类型的"通用"机架）。
:::

:::warning
不同机架之间的映射不一致（例如，不能依赖所有固定翼机架的油门位于同一输出端口）。请务必使用适合您机体的正确映射。
:::

## 其他外设

- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)
- [Holybro Sensors](https://holybro.com/collections/sensors)
- [Holybro GPS & RTK Systems](https://holybro.com/collections/gps-rtk-systems)
- [电源模块 & PDBs](https://holybro.com/collections/power-modules-pdbs)

可选/不常见组件的接线和配置在各个[外设](../peripherals/index.md)主题中有所涵盖。

## 引脚定义

- [Holybro Pixhawk Baseboard 引脚定义](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-baseboard-pinout)
- [Holybro Pixhawk Mini-Baseboard 引脚定义](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-mini-baseboard-pinout)
- [Holybro Pixhawk Jetson Baseboard](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard)
- [Holybro Pixhawk RPi CM4 Baseboard](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-rpi-cm4-baseboard)

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 特定配置请参考此处：[QuadPlane 垂直起降配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->## 进一步信息

- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6X](../flight_controller/pixhawk6x.md) (PX4 Doc Overview page)
- [Pixhawk 6X Pro](../flight_controller/pixhawk6x_pro.md) (PX4 Doc Overview page)
- [Pixhawk Autopilot FMUv6X Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf).
- [Pixhawk Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).