# 无线电控制系统

无线电控制（RC）系统可用于通过手持遥控器**手动**控制您的机体。  
本节内容将概述RC系统的工作原理，如何为您的机体选择合适的无线电系统，以及如何将其连接到飞控器。

:::tip
PX4也可通过[操纵杆](../config/joystick.md)或类似游戏手柄的控制器进行手动控制：这与RC系统不同！  
[COM_RC_IN_MODE](../advanced_config/parameter_reference.md#COM_RC_IN_MODE) 参数 [可以设置](../advanced_config/parameters.md) 以选择启用RC（默认）、操纵杆、两者或都不启用。
:::

::: info
PX4在自主飞行模式下不需要遥控系统。
:::

## 遥控系统如何工作？

一个_遥控系统_包含一个由操作员用来控制机体的地面_遥控单元_。遥控器通过物理控制装置可以指定机体的运动（例如速度、方向、油门、偏航、俯仰、横滚等），并可启用自动驾驶仪的[飞行模式](../flight_modes/index.md)（例如起飞、降落、返航、任务等）。在_支持遥测的_遥控系统中，遥控单元还可以接收并显示来自机体的信息，例如电量、飞行模式和警告。

![Taranis X9D 发射机](../../assets/hardware/transmitters/frsky_taranis_x9d_transmitter.jpg)

地面遥控器内含一个与机体上（兼容的）无线电模块进行绑定和通信的无线电模块。机体上的单元连接到飞控。飞控根据当前自动驾驶仪飞行模式和机体状态确定如何解释指令，并相应驱动机体电机和执行器。

<!-- 插入展示各部分的图片会更好 -->

::: info
地面和机体上的无线电模块分别称为发射机和接收机（即使它们支持双向通信），统称为_发射机/接收机对_。遥控器及其包含的无线电模块通常称为“发射机”。
:::

遥控系统的重要特性是其支持的“通道”数量。通道数量决定了遥控器上多少个物理控制装置可以用于向机体发送指令（例如可以实际使用的开关、旋钮、操纵杆数量）。

一个飞行器必须使用至少支持4个通道的系统（横滚、俯仰、偏航、推力）。地面车辆至少需要两个通道（转向+油门）。8或16通道发射机提供额外的通道，可用于控制其他机制或激活自动驾驶仪提供的不同[飞行模式](../flight_modes/index.md)。

## 遥控器类型

<a id="transmitter_modes"></a>

### 飞机用遥控器单元

无人机最流行的_遥控器形式_如下所示。  
它有独立的操纵杆分别控制滚转/俯仰和油门/偏航（如图所示，即飞机至少需要4个通道）。

![RC基本指令](../../assets/flying/rc_basic_commands.png)

操纵杆、开关等的布局有多种可能性。  
更常见的布局被赋予了特定的"模式"编号。_Mode 1_和_Mode 2_（如下所示）的区别仅在于油门的位置。

![Mode1-Mode2](../../assets/concepts/mode1_mode2.png)

::: info
模式选择主要取决于个人偏好（_Mode 2_更受欢迎）。
:::

## 地面机体的遥控单元

无人驾驶地面机体（UGV）/车至少需要双通道发射机来发送转向和速度的值。发射机通常通过轮盘和扳机、两个单轴控制杆或一个双轴控制杆来设置这些值。

使用更多通道/控制机制没有限制，这些机制可以非常有效地启用更多执行器和自动驾驶模式。

## 选择RC系统组件

您需要选择一套相互兼容的遥控器/接收机组合。  
此外，接收机必须与[与PX4兼容](#compatible_receivers)并兼容飞控硬件。

兼容的无线电系统通常成套出售。  
例如，[FrSky Taranis X9D和FrSky X8R](https://hobbyking.com/en_us/frsky-2-4ghz-accst-taranis-x9d-plus-and-x8r-combo-digital-telemetry-radio-system-mode-2.html?___store=en_us)是热门组合。

### 遥控器/接收机组合

最流行的RC设备之一是_FrSky Taranis X9D_。  
其内置的发射模块可直接配合推荐的_FrSky X4R-SB_（S-BUS，低延迟）或_X4R_（PPM-Sum，传统）接收机使用。  
它还具备自定义无线电发射模块插槽和可定制的开源OpenTX固件。

::: info  
此遥控器单元在使用[FrSky](../peripherals/frsky_telemetry.md)或[TBS Crossfire](../telemetry/crsf_telemetry.md)无线电模块时可显示机体遥测数据。  
:::

其他流行的遥控器/接收机组合：

- 使用FrSky发射/接收模块的Turnigy遥控器
- Futaba遥控器及其兼容的Futaba S-Bus接收机
- 长距离~900MHz，低延迟："Team Black Sheep Crossfire"或"Crossfire Micro"套装，搭配兼容遥控器（如Taranis）
- 长距离~433MHz：ImmersionRC EzUHF套装，搭配兼容遥控器（如Taranis）

### 与PX4兼容的接收机 {#compatible_receivers}

除遥控器/接收机组合兼容外，接收机还必须与PX4和飞控硬件兼容。  
_PX4_和_Pixhawk_已验证支持以下类型：

- PPM总和接收机
- S.BUS和S.BUS2接收机来自：

  - Futaba
  - FrSky S.BUS和PPM型号
  - TBS Crossfire（使用SBUS输出协议）
  - Herelink
- TBS Crossfire（[CRSF协议](../telemetry/crsf_telemetry.md)）
- Express LRS（[CRSF协议](../telemetry/crsf_telemetry.md)）
- TBS Ghost（GHST协议）
- Spektrum DSM
- Graupner HoTT

使用已支持协议的其他厂商接收机可能正常工作但未经测试。

::: info  
历史上接收机型号之间存在差异和不兼容问题，主要由于协议详细规格的缺失。  
我们测试过的接收机现在都已显示兼容，但其他型号可能不兼容。  
:::

## 连接接收机

作为一般指导，接收机通过与其支持协议对应的端口连接到飞控：

- Spektrum/DSM 接收机连接到 "DSM" 输入端口。  
  Pixhawk 飞控将该端口标记为：`SPKT/DSM`、`DSM`、`DSM/SBUS RC`、`DSM RC` 或 `DSM/SBUS/RSSI`。
- Graupner HoTT 接收机：SUMD 输出必须连接到 **SPKT/DSM** 输入端口（如上所述）。
- PPM-Sum 和 S.BUS 接收机必须直接连接到 **RC** 地、电源和信号引脚。  
  通常标记为：`RC IN`、`RCIN` 或 `RC`，但部分飞控标记为 `PPM RC` 或 `PPM`。
- 每个通道使用独立线缆的 PPM 接收机需通过 PPM 编码器连接到 RCIN 通道 [例如此款](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)（PPM-Sum 接收机使用单根信号线传输所有通道）。
- 使用 [CRSF Telemetry](../telemetry/crsf_telemetry.md) 的 TBS Crossfire/Express LRS 接收机通过备用 UART 连接。

飞控通常会配备连接常用接收机类型的线缆。

具体飞控的连接说明请参考其 [快速入门](../assembly/index.md) 指南（例如 [CUAV Pixhawk V6X 接线快速入门：无线电控制](../assembly/quick_start_cuav_pixhawk_v6x.md#radio-control) 或 [Holybro Pixhawk 6X 接线快速入门：无线电控制](../assembly/quick_start_pixhawk6x.md#radio-control)）。

:::tip
更多信息请参阅飞控制造商的设置指南。
:::

<a id="binding"></a>## 绑定遥控器/接收机

在您校准/使用无线电系统之前，必须将接收机和遥控器进行_bind_，以确保它们仅与彼此通信。  
绑定遥控器和接收机的流程因硬件而异（请参阅您的手册获取说明）。  

如果您使用的是_Spektrum_接收机，可以使用_QGroundControl_将其置于绑定模式：[无线电设置 > Spektrum绑定](../config/radio.md#spectrum-bind)。

## 设置信号丢失行为

遥控接收器有不同的方式来表示信号丢失：

- 输出无信号（由PX4自动检测）
- 输出低油门值（您可[配置PX4以检测此情况](../config/radio.md#rc-loss-detection)）。
- 输出最后接收到的信号（PX4无法处理这种情况！）

选择在遥控丢失时能够输出无信号（首选）或输出低油门值的接收器。此行为可能需要对接收器进行硬件配置（请参阅手册）。

有关更多信息，请参见 [无线电控制设置 > 信号丢失检测](../config/radio.md#rc-loss-detection)。

## 相关主题

- [遥控器设置](../config/radio.md) - 使用 PX4 配置遥控器。
- 在 [多旋翼](../flying/basic_flying_mc.md) 或 [固定翼](../flying/basic_flying_fw.md) 上手动飞行 - 学习如何通过遥控器飞行。
- [TBS Crossfire (CRSF) 遥测](../telemetry/crsf_telemetry.md)
- [FrSky 遥测](../peripherals/frsky_telemetry.md)