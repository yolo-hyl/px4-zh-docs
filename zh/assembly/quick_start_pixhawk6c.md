# Holybro Pixhawk 6C 接线快速入门

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

本快速入门指南展示了如何为[Pixhawk 6C<sup>&reg;</sup>](../flight_controller/pixhawk6c.md)飞行控制器供电并连接其最重要的外设。

## 套件内容

Pixhawk 6C + PM02 + M8N GPS。

![Pixhawk6c 标准套件](../../assets/flight_controller/pixhawk6c/pixhawk6c_standard_set.jpg)

## 安装和定位控制器

_Pixhawk 6C_ 可使用套件中包含的双面胶带安装在机架上。
应尽可能靠近机体重心位置安装，顶部朝上，箭头指向机体前方。

<img src="../../assets/flight_controller/pixhawk6c/pixhawk6c_vehicle_front1.jpg" width="300px" title="Pixhawk6c - 安装方向" />

::: info
如果控制器无法以推荐/默认方向安装（例如由于空间限制），则需要使用实际安装方向配置自动驾驶仪软件：[飞行控制器方向](../config/flight_controller_orientation.md)。
:::

## GPS + 磁力计 + 蜂鸣器 + 安全开关 + LED

_Pixhawk6C_ 可配用 M8N 或 M9N GPS（10 针连接器），应连接到 **GPS1** 端口。
这些 GNSS 模块集成了磁力计、安全开关、蜂鸣器和 LED。

可单独购买的 [M8N 或 M9N GPS](https://holybro.com/collections/gps)（6 针连接器）可连接到 **GPS2** 端口。

GPS/磁力计应[安装在机架上](../assembly/mount_gps_compass.md)，尽可能远离其他电子设备，方向标记朝向机体前方（将磁力计与其他电子设备分开可减少干扰）。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_gps_front.jpg" width="200px" title="Pixhawk5x 标准套件" />

::: info
GPS 模块集成的安全开关默认启用（启用时，PX4 不会允许你解锁机体）。
要禁用安全开关，请按住安全开关 1 秒。
再次按下安全开关可启用安全功能并解锁机体（如果由于某种原因无法通过遥控器或地面站解锁机体，此功能非常有用）。
:::

## 电源

将标准套件中包含的电源模块输出通过 6 芯线缆连接到 _Pixhawk 6C_ 的 **POWER** 端口。

如果使用固定翼或地面车，**FMU PWM-OUT** 需要单独供电以驱动舵机（方向舵、升降舵等）。
这可以通过将 **FMU PWM-OUT** 的 8 针电源（+）轨连接到电压调节器（例如，带 BEC 的电调、独立 5V BEC 或 2S 锂电池）来实现。

::: info
电源轨电压必须与所用舵机兼容！
:::

| PIN & 连接器 | 功能                                            |
| --------------- | --------------------------------------------------- |
| I/O PWM Out     | 在此处连接电机信号和 GND 线。            |
| FMU PWM Out     | 在此处连接舵机信号、正极和 GND 线。  |

::: info
PX4 固件中的 **MAIN** 输出映射到 _Pixhawk 6C_ 的 **I/O PWM OUT** 端口，而 **AUX 输出** 映射到 **FMU PWM OUT**。
例如，**MAIN1** 映射到 **I/O PWM OUT** 的 IO_CH1 引脚，**AUX1** 映射到 **FMU PWM OUT** 的 FMU_CH1 引脚。
:::

_Pixhawk 6C_ 电源端口的引脚分配如下：

| 引脚      | 信号  | 电压  |
| -------- | ------- | ----- |
| 1(红色)   | VDD     | +5V   |
| 2(黑色) | VDD     | +5V   |
| 3(黑色) | CURRENT | +3.3V |
| 4(黑色) | VOLTAGE | +3.3V |
| 5(黑色) | GND     | GND   |
| 6(黑色) | GND     | GND   |

## 遥控

如果需要手动控制机体（PX4 在自主飞行模式下不需要无线电系统），则需要远程控制（RC）无线电系统。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver_selection.md)，并进行绑定。
注意区分 DSM、PPM/SBUS 等术语，保留不翻译。
同时，PPM 编码器的链接要保留，确保用户能找到购买信息。

## 遥测无线电（可选）

可选的遥测无线电部分，但需要保留链接到 Holybro 的购买页面。

## SD 卡

强调推荐使用，并说明插入位置，图片链接和尺寸参数要保留。info 提示链接到基本概念部分，确保用户了解 SD 卡的作用。

## 电机连接

明确说明 MAIN 和 AUX 端口的用途，以及不同机型的映射差异，警告部分强调不同机型的配置不同，必须使用正确的映射。

## 其他外设

链接到周边设备文档，保持链接结构。

## 引脚图和配置

保留链接到官方文档和标准文件。需要检查所有链接是否正确，确保用户能顺利访问。

## 更多信息

- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6C](../flight_controller/pixhawk6c.md) (PX4 文档概览页)
- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [Pixhawk 自动驾驶仪总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).