# _Pixhawk<sup>&reg;</sup> 4 Mini_ 接线快速入门

:::warning
PX4 并不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

本快速入门指南介绍了如何为 [_Pixhawk<sup>&reg;</sup> 4 Mini_](../flight_controller/pixhawk4_mini.md) 飞控供电，并连接其最重要的外围设备。

![Pixhawk4 mini](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_iso_1.png)

## 接线图概述

下图展示了除电机和舵机外最重要的传感器和外设的连接位置。

![*Pixhawk 4 Mini* 接线总览](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_wiring_overview.png)

:::tip
有关可用端口的更多信息，请参见此处: [_Pixhawk 4 Mini_ > Interfaces](../flight_controller/pixhawk4_mini.md#interfaces).
:::

## 安装和调整控制器方向

_Pixhawk 4 Mini_ 应使用套件中包含的减震泡沫垫安装在机架上。  
应将其尽可能靠近机体的重心位置，朝上放置且箭头指向机体前方。

![*Pixhawk 4 Mini* 方向](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_orientation.png)

::: info
如果控制器无法以推荐/默认方向安装（例如由于空间限制），则需要根据实际使用的方向配置自动驾驶仪软件：[飞行控制器方向](../config/flight_controller_orientation.md)。
:::

## GPS + 指南针 + 蜂鸣器 + 安全开关 + LED

将提供的集成指南针、安全开关、蜂鸣器和LED的GPS连接到**GPS模块**接口。GPS/指南针应[安装在机架](../assembly/mount_gps_compass.md)上，尽可能远离其他电子设备，方向标记朝向机体前方（将指南针与其他电子设备分离可减少干扰）。

![连接指南针/GPS到Pixhawk 4](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_gps.png)

::: info
GPS模块的集成安全开关默认启用（启用时，PX4将不允许你解锁机体）。
要禁用安全开关，请按住安全开关1秒。
再次按下安全开关可启用安全功能并上锁机体（这在某些情况下可能有用，例如你无法通过遥控器或地面站上锁机体时）。
:::

## 电源

电源管理板（PMB）同时具备电源模块和配电板的功能。除了为 _Pixhawk 4 Mini_ 和电调提供稳压电源外，它还会向飞控发送电池电压和电流信息。

使用6芯线缆将套件附带的PMB输出端连接到 _Pixhawk 4 Mini_ 的 **POWER** 接口。PMB的连接方式（包括对电调和舵机的电源/信号连接）如下图所示。

![Pixhawk 4 - Power Management Board](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_power_management.png)

::: info
上图仅显示了单个电调和单个舵机的连接方式。其余电调和舵机应采用相同方式连接。
:::

| 引脚或连接器 | 功能描述 |
| ------------------- | -------------------------------------------------------------------------- |
| B+                  | 连接电调B+为电调供电 |
| GND                 | 连接电调接地 |
| PWR                 | JST-GH 6针接口，5V 3A输出<br>连接到 _Pixhawk 4 Mini_ POWER |
| BAT                 | 电源输入，连接2~12S锂电池 |

_Pixhawk 4 Mini_ **POWER** 接口的引脚定义如下：
`CURRENT` 信号默认应提供0-3.3V模拟电压（对应0-120A）。
`VOLTAGE` 信号默认应提供0-3.3V模拟电压（对应0-60V）。
VCC线路需至少支持3A持续电流，标准电压为5.1V。5V较低电压仍可接受但不推荐。

| 引脚 | 信号 | 电压 |
| -------- | ------- | ----- |
| 1(红色)   | VCC     | +5V   |
| 2(黑色) | VCC     | +5V   |
| 3(黑色) | CURRENT | +3.3V |
| 4(黑色) | VOLTAGE | +3.3V |
| 5(黑色) | GND     | GND   |
| 6(黑色) | GND     | GND   |

::: info
如果使用固定翼或地面车，**MAIN OUT** 的8针电源(+）轨道需要单独供电以驱动舵机（如方向舵、升降舵等）。为此需要将电源轨道连接到带BEC的电调、独立5V BEC或2S锂电池。请注意此处所用舵机的电压限制。
:::

::: info
使用套件附带的电源模块时，需要在[Power Settings](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/power.html)中配置 _Cell Count_，但无需校准 _voltage divider_。若使用其他电源模块（如Pixracer的模块），则需要更新 _voltage divider_ 设置。
:::

## 无线电控制

若要_手动_控制你的机体（PX4在自主飞行模式下不需要无线电系统），你必须配备一个远程控制（RC）无线电系统。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)并进行_绑定_以建立通信（请阅读随附的发射机/接收机说明书）。

以下说明展示了如何将不同类型的接收机连接到_Pixhawk 4 Mini_：

- Spektrum/DSM或S.BUS接收机连接到 **DSM/SBUS RC** 输入。

  ![Pixhawk 4 Mini - Spektrum接收机的无线电端口](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_rc_dsmsbus.png)

- PPM接收机连接到 **PPM RC** 输入端口。

  ![Pixhawk 4 Mini - PPM接收机的无线电端口](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_rc_ppm.png)

- 对于具有_每个通道独立线缆_的PPM和PWM接收机，必须通过PPM编码器[例如此型号](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)连接到 **PPM RC** 端口（PPM-Sum接收机使用单个信号线传输所有通道）。

有关选择无线电系统、接收机兼容性以及绑定发射机/接收机对的更多信息，请参阅：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

遥测无线电可用于从地面站与机体通信并控制飞行（例如，您可以指挥UAV到达特定位置，或上传新任务）。

机体端的无线电应连接到**TELEM1**端口（如图所示），如果连接到该端口则无需进一步配置。
另一台无线电连接到您的地面站计算机或移动设备（通常通过USB连接）。

![Pixhawk 4 Mini Telemetry](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_telemetry.png)

## microSD 卡（可选）

强烈建议使用 SD 卡，因为需要它来[记录和分析飞行细节](../getting_started/flight_reporting.md)，运行任务，并使用 UAVCAN-bus 硬件。
将卡（包含在套件中）插入 _Pixhawk 4 Mini_，如下所示。

![Pixhawk 4 Mini SD 卡](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_sdcard.png)

:::tip
更多信息请参见[基础概念 > SD 卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机按照您机体在[机架参考](../airframes/airframe_reference.md)中指定的顺序连接到**MAIN OUT**端口。有关更多细节，请参阅[_Pixhawk 4 Mini_ > 支持的平台](../flight_controller/pixhawk4_mini.md#supported-platforms)。

::: info
此参考文档列出了所有支持的飞机和地面机架的输出端口到电机/舵机映射（如果您的机架未在参考文档中列出，请使用相应类型的"通用"机架）。
:::

:::warning
不同机架之间的映射并不一致（例如，您不能依赖所有飞机机架的油门都位于同一输出端口）。
请确保为您的机体使用正确的映射。
:::

## 其他外设

可选/不常见组件的接线和配置在各个[外设](../peripherals/index.md)的主题中进行了介绍。

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 特定配置请参考：[QuadPlane VTOL 配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->## 更多信息

- [_Pixhawk 4 Mini_](../flight_controller/pixhawk4_mini.md)