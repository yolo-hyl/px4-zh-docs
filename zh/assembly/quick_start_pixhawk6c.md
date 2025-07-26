

# Holybro Pixhawk 6C 接线快速入门

:::warning
PX4 并不生产此款（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

本快速入门指南介绍如何为 [Pixhawk 6C<sup>&reg;</sup>](../flight_controller/pixhawk6c.md) 飞行控制器供电，并连接其最重要的外围设备。

## 套件内容

Pixhawk 6C + PM02 + M8N GPS。

![Pixhawk6c 标准套装](../../assets/flight_controller/pixhawk6c/pixhawk6c_standard_set.jpg)

## 安装和调整控制器方向

_Pixhawk 6C_ 可通过套件中包含的双面胶带安装在机架上。
应尽可能靠近机体的重心位置安装，顶部朝上且箭头指向机体前方。

<img src="../../assets/flight_controller/pixhawk6c/pixhawk6c_vehicle_front1.jpg" width="300px" title="Pixhawk6c - 安装方向" />

::: info
如果控制器无法以推荐/默认方向安装（例如因空间限制），则需要根据实际使用的方向配置自动驾驶仪软件：[飞行控制器方向](../config/flight_controller_orientation.md)。
:::

## GPS + 陀螺仪 + 蜂鸣器 + 安全开关 + LED

_Pixhawk6C_ 可搭配 M8N 或 M9N GPS（10针接口）购买，应将其连接到 **GPS1** 接口。  
这些GNSS模块集成了陀螺仪、安全开关、蜂鸣器和LED。

可单独购买并连接到 **GPS2** 接口的 [M8N 或 M9N GPS](https://holybro.com/collections/gps)（6针接口）。

GPS/陀螺仪应按照[机架安装指南](../assembly/mount_gps_compass.md)安装在机体尽可能远离其他电子设备的位置，且方向标识应指向机体前方（将陀螺仪与其他电子设备分离可减少干扰）。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_gps_front.jpg" width="200px" title="Pixhawk5x 标准套件" />

::: info
GPS模块的集成安全开关默认启用（启用状态下，PX4将不允许你解锁机体）。  
要禁用安全开关，请按住安全开关1秒。  
你可以再次按下安全开关以启用安全功能并解除机体锁定（这在某些情况下非常有用，例如你因任何原因无法通过遥控器或地面站解除机体锁定时）。
:::

## 电源

将标准套件中电源模块的输出通过六芯线缆连接至 _Pixhawk 6C_ 的 **POWER** 接口。

若使用固定翼或地面车，需单独为 **FMU PWM-OUT** 供电以驱动舵机（如方向舵、升降舵等）。可通过将 **FMU PWM-OUT** 的 8 针电源（+）轨连接至电压调节器实现（例如带 BEC 的电调、独立 5V BEC 或 2S 锂电）。

::: info
电源轨电压必须与所使用的舵机相匹配！
:::

| 引脚与连接器 | 功能                                  |
| ------------ | ------------------------------------- |
| I/O PWM Out  | 在此连接电机信号线和 GND 线。         |
| FMU PWM Out  | 在此连接舵机信号线、正电源线和 GND 线。|

::: info
PX4 固件中的 **MAIN** 输出对应 _Pixhawk 6C_ 的 **I/O PWM OUT** 接口，而 **AUX 输出** 对应 **FMU PWM OUT** 接口。
例如，**MAIN1** 对应 **I/O PWM OUT** 的 IO_CH1 引脚，**AUX1** 对应 **FMU PWM OUT** 的 FMU_CH1 引脚。
:::

_Pixhawk 6C_ 电源接口的引脚定义如下所示。

| 引脚       | 信号   | 电压  |
| ---------- | ------ | ----- |
| 1(红色)    | VDD    | +5V   |
| 2(黑色)    | VDD    | +5V   |
| 3(黑色)    | CURRENT| +3.3V |
| 4(黑色)    | VOLTAGE| +3.3V |
| 5(黑色)    | GND    | GND   |
| 6(黑色)    | GND    | GND   |

## 遥控

如果你想_手动_控制机体（PX4在自主飞行模式下不需要遥控系统），则需要使用遥控（RC）遥控系统。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后将它们进行_绑定_以实现通信（请阅读随附的特定发射机/接收机说明）。

- Spektrum/DSM接收机连接到**DSM**输入。
- PPM或SBUS接收机连接到**PPM/SBUS**输入端口。

具有_每个通道独立引线_的PPM和PWM接收机必须通过PPM编码器连接到*PPM/SBUS**端口 [例如这一款](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)（PPM-Sum接收机使用单根信号线传输所有通道）。

欲了解更多关于选择遥控系统、接收机兼容性以及绑定发射机/接收机对的信息，请参见：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

[遥测无线电](../telemetry/index.md)可用于从地面站对飞行中的机体进行通信和控制（例如，您可以引导无人机到特定位置，或上传新的任务）。

机体上的无线电应连接到 **TELEM1** 端口（如连接到此端口，则无需进一步配置）。
另一个无线电连接到您的地面站计算机或移动设备（通常通过USB连接）。

您还可以在 [Holybro的网站](https://holybro.com/collections/telemetry-radios) 购买相关无线电设备。

## SD 卡（可选）

推荐使用 SD 卡，因为它们需要用于[记录和分析飞行细节](../getting_started/flight_reporting.md)、执行任务以及使用 UAVCAN-bus 硬件。  
将卡片（包含在 Pixhawk 6C 中）按照下图所示插入 _Pixhawk 6C_。

<img src="../../assets/flight_controller/pixhawk6c/pixhawk6c_sd_slot.jpg" width="320px" title="Pixhawk6c SD" />

:::tip
欲了解更多详情，请参见 [基本概念 > SD 卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机按照[机架参考](../airframes/airframe_reference.md)中为您的机体指定的顺序连接到 **I/O PWM OUT** (**MAIN**) 和 **FMU PWM OUT** (**AUX**) 接口。

::: info
该参考列出了所有支持的空中和地面机架的输出端口到电机/舵机的映射关系（如果您的机架未在参考中列出，则使用对应类型的"通用"机架）。
:::

:::warning
不同机架之间的映射不一致（例如，您不能指望推力器在所有飞机机架上都使用相同的输出）。请确保为您的机体使用正确的映射。
:::

## 其他外设

可选/不常见组件的布线和配置包含在各个[外设](../peripherals/index.md)的主题中。

## 引脚分配

- [Holybro Pixhawk -6C 引脚分配](https://docs.holybro.com/autopilot/pixhawk-6c/pixhawk-6c-pinout)

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)。

QuadPlane 特定配置请参考此处：[QuadPlane 垂直起降配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->

## 进一步信息

- [Holybro 文档](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6C](../flight_controller/pixhawk6c.md) (PX4 Doc Overview page)
- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [Pixhawk 自主导航总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).