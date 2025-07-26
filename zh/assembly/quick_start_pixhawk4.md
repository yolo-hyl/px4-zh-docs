# Pixhawk 4 接线快速入门

:::warning
PX4 并不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

本快速入门指南展示了如何为 [Pixhawk 4](../flight_controller/pixhawk4.md)<sup>&reg;</sup> 飞控供电并连接其最重要的外设。

<img src="../../assets/flight_controller/pixhawk4/pixhawk4_logo_view.jpg" width="420px" title="Pixhawk4 图像" />

## 接线图概览

下图展示了如何连接最重要的传感器和外设（除了电机和舵机的输出）。我们将在接下来的章节中逐一详细说明这些内容。

![Pixhawk 4 Wiring Overview](../../assets/flight_controller/pixhawk4/pixhawk4_wiring_overview.png)

:::tip
关于可用端口的更多信息请参见此处：[Pixhawk 4 > Connections](../flight_controller/pixhawk4.md#connectors)。
:::

## 安装和调整控制器

_Pixhawk 4_ 应使用减震泡沫垫（套件包含）安装在机架上。应尽可能将控制器安装在机体重心附近，保持顶面朝上，箭头指向机体前方。

<img src="../../assets/flight_controller/pixhawk4/pixhawk4_mounting_and_foam.png" align="center"/>

::: info
如果控制器无法以推荐/默认方向安装（例如因为空间限制），您需要在自动驾驶软件中配置实际使用的方向：[飞行控制器方向](../config/flight_controller_orientation.md)。
:::

## GPS + 指南针 + 蜂鸣器 + 安全开关 + LED

将集成指南针、安全开关、蜂鸣器和LED的GPS模块连接到 **GPS 模块** 接口。

GPS/指南针应尽量[安装在机体框架上](../assembly/mount_gps_compass.md)，远离其他电子设备，方向标记朝向机体前方（将指南针与其他电子设备隔开可减少干扰）。

![将指南针/GPS连接到Pixhawk 4](../../assets/flight_controller/pixhawk4/pixhawk4_compass_gps.jpg)

::: info
GPS模块集成的安全开关默认启用（启用时，PX4将不允许启动机体）。
要禁用安全功能，请按住安全开关1秒。
再次按下安全开关可启用安全功能并关闭机体（当您因任何原因无法通过遥控器或地面站关闭机体时，此功能非常有用）。
:::

## 电源

将套件中提供的_电源管理板_(PM板)的输出连接到_Pixhawk 4_的**POWER**接口中，使用六芯线缆进行连接。
PM板的输入**2~12S**将连接到你的锂聚合物电池。
电源管理板的连接方式，包括对电调和舵机的供电与信号连接，详见下表说明。
注意：电源管理板不会通过**FMU PWM-OUT**的+和-引脚为舵机供电。

下图展示了_Pixhawk 4_配套的电源管理板。

![Pixhawk 4 - 电源管理板](../../assets/hardware/power_module/holybro_pm07/pixhawk4_power_management_board.png)

::: info
如果使用固定翼或地面车辆，需要单独为**FMU PWM-OUT**的8针电源(+)轨供电，以驱动舵机(如方向舵、升降舵等)。
为此，电源轨需连接配备BEC的电调或独立5V BEC电源或2S锂聚合物电池。
此处需注意所使用舵机的电压。
:::

| 引脚/连接器 | 功能描述                                                                 |
| ----------- | ------------------------------------------------------------------------ |
| I/O PWM-IN  | 参见下文关于连接至_Pixhawk 4_的说明                                      |
| M1          | I/O PWM输出1：在此处将信号线连接到电机1的电调                             |
| M2          | I/O PWM输出2：在此处将信号线连接到电机2的电调                             |
| M3          | I/O PWM输出3：在此处将信号线连接到电机3的电调                             |
| M4          | I/O PWM输出4：在此处将信号线连接到电机4的电调                             |
| M5          | I/O PWM输出5：在此处将信号线连接到电机5的电调                             |
| M6          | I/O PWM输出6：在此处将信号线连接到电机6的电调                             |
| M7          | I/O PWM输出7：在此处将信号线连接到电机7的电调                             |
| M8          | I/O PWM输出8：在此处将信号线连接到电机8的电调                             |
| FMU PWM-IN  | 参见下文关于连接至_Pixhawk 4_的说明                                      |
| FMU PWM-OUT | 如果FMU PWM-IN连接至_Pixhawk 4_，在此处将信号线连接到电调或舵机，+、-线连接到舵机 |
| CAP&ADC-OUT | 连接到_Pixhawk 4_的CAP & ADC IN端口                                     |
| CAP&ADC-IN  | CAP&ADC输入：板背面印有引脚定义                                         |
| B+          | 连接到电调的B+以供电给电调                                               |
| GND         | 连接到电调的接地端                                                       |
| PWR1        | 5V输出3A，连接到_Pixhawk 4_的POWER 1                                     |
| PWR2        | 5V输出3A，连接到_Pixhawk 4_的POWER 2                                     |
| 2~12S       | 电源输入，连接到12S锂聚合物电池                                          |

::: info
根据你的机体类型，请参考[机体参考文档](../airframes/airframe_reference.md)将_Pixhawk 4_的**I/O PWM OUT**和**FMU PWM OUT**端口连接到PM板。
PX4固件中的**MAIN**输出对应_Pixhawk 4_的**I/O PWM OUT**端口，而**AUX输出**对应**FMU PWM OUT**端口。
例如，**MAIN1**对应**I/O PWM OUT**的IO_CH1引脚，**AUX1**对应**FMU PWM OUT**的FMU_CH1引脚。PM板的**FMU PWM-IN**内部连接到**FMU PWM-OUT**。PM板的**I/O PWM-IN**内部连接到**M1-8**。
:::

下表总结了根据机体参考文档，如何将_Pixhawk 4_的PWM OUT端口连接到PM板的PWM-IN端口。

| 机体参考类型 | _Pixhawk 4_ --> PM板连接方式 |
| ------------ | ---------------------------- |
| **MAIN**: 电机 | I/O PWM OUT --> I/O PWM IN  |
| **MAIN**: 舵机 | I/O PWM OUT --> FMU PWM IN  |
| **AUX**: 电机  | FMU PWM OUT --> I/O PWM IN  |
| **AUX**: 舵机  | FMU PWM OUT --> FMU PWM IN  |

<!--未来当Pixhawk 4套件可用时，添加不同机体的接线图/视频。-->

_Pixhawk 4_电源接口的引脚定义如下所示。
CURRENT信号默认应传输0-3.3V电压范围，对应0-120A电流。
VOLTAGE信号默认应传输0-3.3V电压范围，对应0-60V电压。
VCC线路必须至少提供3A持续电流，且默认为5.1V。
5V电压仍可接受，但不推荐。

| 引脚     | 信号   | 电压   |
| -------- | ------ | ------ |
| 1(红色)  | VCC    | +5V    |
| 2(黑色)  | VCC    | +5V    |
| 3(黑色)  | CURRENT | +3.3V  |
| 4(黑色)  | VOLTAGE | +3.3V  |
| 5(黑色)  | GND    | GND    |
| 6(黑色)  | GND    | GND    |

::: info
使用套件中提供的电源模块时，需要在[电源设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/power.html)中配置_电池节数_，但不需要校准_电压分压器_。
如果使用其他电源模块(例如Pixracer的模块)，则需要更新_电压分压器_配置。
:::

## 遥控

如果需要_手动_控制机体(PX4在自主飞行模式下不依赖遥控系统)，则必须配备遥控系统。

您需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后将它们进行_绑定_以建立通信(请阅读您所选发射机/接收机附带的说明手册)。

以下说明展示了如何将不同类型的接收机连接到_Pixhawk 4_：

- Spektrum/DSM或S.BUS接收机应连接至 **DSM/SBUS RC** 输入端口。

  ![Pixhawk 4 - Spektrum接收机使用的遥控端口](../../assets/flight_controller/pixhawk4/pixhawk4_receiver_sbus.png)

- PPM接收机应连接至 **PPM RC** 输入端口。

  ![Pixhawk 4 - PPM接收机使用的遥控端口](../../assets/flight_controller/pixhawk4/pixhawk_4_receiver_ppm.png)

- 具有_每个通道独立线缆_的PPM和PWM接收机必须通过PPM编码器连接到 **PPM RC** 端口[例如此产品](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html) (PPM-Sum接收机为所有通道共用单条信号线)。

有关选择遥控系统、接收机兼容性及绑定发射机/接收机对的更多信息，请参见：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

遥测无线电可用于从地面站对机体进行通信和飞行控制（例如，您可以指导无人机飞往特定位置，或上传新的任务）。

机体端的无线电应连接到 **TELEM1** 端口（如图所示，若连接至此端口则无需进一步配置）。另一部无线电连接至您的地面站计算机或移动设备（通常通过USB连接）。

![Pixhawk 4/Telemetry Radio](../../assets/flight_controller/pixhawk4/pixhawk4_telemetry_radio.jpg)

<a id="sd_card"></a>

## SD卡（可选）

强烈建议使用SD卡，因为需要它来[记录和分析飞行细节](../getting_started/flight_reporting.md)，运行任务，以及使用UAVCAN-bus硬件。
将卡（包含在Pixhawk 4套件中）插入_Pixhawk 4_，如下图所示。

![Pixhawk 4/SD卡](../../assets/flight_controller/pixhawk4/pixhawk4_sd_card.png)

:::tip
更多信息请参阅[基本概念 > SD卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机应按照[机体参考](../airframes/airframe_reference.md)中为机体指定的顺序连接到 **I/O PWM OUT** (**MAIN**) 和 **FMU PWM OUT** (**AUX**) 端口。

::: info
此参考文档列出了所有支持的飞行器和地面平台的输出端口与电机/舵机映射关系（如果您的机体未在参考中列出，则使用对应类型的"generic"通用机体）。
:::

:::warning
不同机体的映射关系不一致（例如，您不能依赖所有飞机机体的油门信号都在同一输出端口）。请务必为您的机体使用正确的映射关系。
:::

## 其他外设

可选的/不太常见的组件的接线和配置在各个 [peripherals](../peripherals/index.md) 的主题中进行了说明。

## 引脚配置

[Pixhawk 4 Pinouts](https://holybro.com/manual/Pixhawk4-Pinouts.pdf) (Holybro)

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 特定配置请参考：[QuadPlane VTOL配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->

## 更多信息

- [Pixhawk 4](../flight_controller/pixhawk4.md) (概览页面)
- [Pixhawk 4技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4/pixhawk4_technical_data_sheet.pdf)
- [Pixhawk 4引脚分配](https://holybro.com/manual/Pixhawk4-Pinouts.pdf) (Holybro)
- [Pixhawk 4快速入门指南 (Holybro)](https://holybro.com/manual/Pixhawk4-quickstartguide.pdf)