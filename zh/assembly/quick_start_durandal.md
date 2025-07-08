# Durandal 连接快速入门

<Badge type="tip" text="PX4 v1.11" />

:::warning
PX4 不制造此（或任何）自动驾驶仪。
请联系[制造商](https://holybro.com/)获取硬件支持或合规问题。
:::

本快速入门指南展示了如何为 Holybro [Durandal](../flight_controller/durandal.md)<sup>&reg;</sup> 飞行控制器供电并连接其最重要的外设。

![Durandal](../../assets/flight_controller/durandal/durandal_hero.jpg)

## 开箱

Durandal 提供了多种配件组合，包括电源模块：_PM02 V3_ 和 _PM07_，以及 _Pixhawk 4 GPS/Compass_ (u-blox NEO-M8N)。

包含 _PM02 V3_ 电源模块的包装内容如下（包装内还包括引脚图和电源模块说明书）。

![Durandal Box](../../assets/flight_controller/durandal/durandal_unboxing_schematics.jpg)

## 接线图概述

下图展示了如何连接最重要的传感器和外设（不包括电机和舵机输出）。我们将在后续章节中逐一详细说明这些内容。

![Durandal Wiring Overview](../../assets/flight_controller/durandal/durandal_wiring_overview.jpg)

:::tip
更多关于可用端口的信息请参考此处：[Durandal > Pinouts](../flight_controller/durandal.md#pinouts)。
:::

## 安装和调整控制器方向

_Durandal_ 应安装在机架上，并尽可能靠近机体的重心位置，方向为正面朝上，箭头指向机体前方。

![Mounting/Orientation](../../assets/flight_controller/durandal/orientation.jpg)

如果控制器无法以推荐/默认方向安装（例如由于空间限制），则需要根据实际安装方向配置自动驾驶软件：[Flight Controller Orientation](../config/flight_controller_orientation.md)。

:::tip
该电路板具有内部减震功能。
不要使用减震泡沫来安装控制器（通常双面胶带就足够）。
:::

## GPS + 指南针 + 蜂鸣器 + 安全开关 + LED

Durandal 专为与 _Pixhawk 4 GPS模块_ 良好兼容而设计，该模块集成了指南针、安全开关、蜂鸣器和 LED。  
它通过 10 针电缆直接连接到 [GPS端口](../flight_controller/durandal.md#gps)。

GPS/指南针应安装在机体上尽可能远离其他电子设备的位置，并将方向标记指向机体前方（将指南针与其他电子设备分离可减少干扰）。

![连接指南针/GPS到Durandal](../../assets/flight_controller/durandal/connection_gps_compass.jpg)

::: info
GPS模块的集成安全开关默认启用（启用时，PX4将不允许您解锁机体）。  
要禁用安全开关，请按住安全开关 1 秒。  
您可以再次按下安全开关以启用安全功能并禁用机体（如果由于任何原因无法通过遥控器或地面站禁用机体，这会很有用）。  
:::

## 电源

您可以使用电源模块或电源分配板为电机/舵机供电并测量功耗。
推荐的电源模块如下所示。

<a id="pm02_v3"></a>

### PM02 v3 电源模块

[电源模块 (PM02 v3)](../power_module/holybro_pm02.md) 可与 _Durandal_ 一起使用。
它为飞控提供稳压电源，并将电池电压/电流传输至飞控。

按照下图连接电源模块的输出。

![Durandal PM02v3 电源连接](../../assets/flight_controller/durandal/connection_power.jpg)

- PM电压/电流端口：使用随附的6线GH线缆连接到 [POWER1](../flight_controller/durandal.md#power) 端口（或 `POWER2`）。
- PM输入（XT60公头）：连接到锂聚合物电池（2~12S）。
- PM电源输出（XT60母头）：连接到任意电机电调。

:::tip
由于该电源模块不包含电源分配线路，通常需要将所有电调并联连接到电源模块输出端（电调必须适用于所提供的电压等级）。
:::

:::tip
**MAIN/AUX** 的8针电源（+）总线不由电源模块为飞控供电。
如果需要单独供电以驱动舵机（如方向舵、升降舵等），需将电源总线连接到带BEC的电调、独立5V BEC或2S锂聚合物电池。
请确保所使用的舵机电压等级合适。
:::

该电源模块具有以下特性/限制：

- 最大输入电压：60V
- 最大电流检测：120A 电压
- 电流测量配置为SV ADC 开关稳压器输出5.2V，最大3A
- 重量：20g
- 包装包含：
  - PM02电路板
  - 6针MLX线缆（1条）
  - 6针GH线缆（1条）

<a id="pm07"></a>

### Pixhawk 4 电源模块（PM07）

[Pixhawk 4 电源模块（PM07）](https://holybro.com/collections/power-modules-pdbs/products/pixhawk-4-power-module-pm07) 可与 _Durandal_ 一起使用。
它同时作为电源模块和电源分配板，为飞控和电调提供稳压电源，并将电池电压/电流传输至飞控。

接线方式与 [Pixhawk 4 快速入门 > 电源](../assembly/quick_start_pixhawk4.md#power) 文档中描述相同。

其具有以下特性/限制：

- PCB电流：总输出120A（最大）
- UBEC 5V输出电流：3A
- UBEC输入电压：7~51V（2~12S锂聚合物电池）
- 尺寸：68*50*8 mm
- 安装孔：45\*45mm
- 重量：36g
- 包装包含：
  - PM07电路板（1块）
  - 80mm XT60连接线（1条）

::: info
另请参见 [PM07 快速入门指南](https://docs.holybro.com/power-module-and-pdb/power-module/pm07-quick-start-guide)（Holybro）。
:::

### 电池配置

电池/电源设置必须在 [电池估计调参](../config/battery.md) 中配置。
对于任一电源模块，都需要配置 _电池电芯数量_ 。

除非使用其他电源模块（例如Pixracer的模块），否则无需更新 _电压分压器_ 。

## 遥控

如果你想_手动_控制你的机体（PX4在自主飞行模式下不需要遥控系统），则需要使用遥控（RC）系统。

你需要[选择兼容的遥控发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后_配对_它们以建立通信（阅读随附的特定遥控发射机/接收机说明书）。

以下说明展示了不同类型的接收机如何连接到_Durandal_：

- Spektrum/DSM接收机连接到[DSM RC](../flight_controller/durandal.md#dsm-rc-port)输入。

  ![Durandal - DSM](../../assets/flight_controller/durandal/dsm.jpg)

- PPM和S.Bus接收机连接到[SBUS_IN/PPM_IN](../flight_controller/durandal.md#rc-in)输入端口（标记为RC IN，位于MAIN/AUX输入旁边）。

  ![Durandal - 背面引脚图（原理图）](../../assets/flight_controller/durandal/durandal_pinouts_back.jpg)

- 对于每个通道各有一根独立导线的PPM和PWM接收机，必须通过PPM编码器[如这个型号](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)连接到**PPM RC**端口（PPM-Sum接收机使用单个信号线传输所有通道）。

有关选择遥控系统、接收机兼容性以及配对遥控发射机/接收机的更多信息，请参阅：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电(可选)

遥测无线电可用于从地面站与机体进行通信和控制(例如，您可以指导UAV飞到特定位置，或上传新任务)。

机体侧的无线电应连接到[TELEM1](../flight_controller/durandal.md#telem1_2_3)端口，使用6位连接器之一(如连接到此端口，无需进一步配置)。
另一个无线电连接到您的地面站计算机或移动设备(通常通过USB连接)。

![Durandal/Telemetry Radio](../../assets/flight_controller/durandal/holybro_telemetry_radio.jpg)

## SD卡（可选）

推荐使用SD卡，因为它们用于记录和分析飞行细节、执行任务以及使用UAVCAN-bus硬件。
将SD卡插入下方所示的_Durandal_中。

![Durandal SD卡](../../assets/flight_controller/durandal/durandal_sd_slot.jpg)

:::tip
更多信息请参见[基本概念 > SD卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机控制信号按照机体在[Airframe Reference](../airframes/airframe_reference.md)中指定的顺序连接到 **I/O PWM OUT** (**MAIN OUT**) 和 **FMU PWM OUT** (**AUX**) 端口。

![Durandal - 背部接线图](../../assets/flight_controller/durandal/durandal_pinouts_back.jpg)

电机需要[单独供电](#power)。

::: info
如果您的机体未在机架参考中列出，请使用类型正确的"通用"机架。
:::

:::tip
_Durandal_ 有5个AUX端口，因此不能用于将AUX6、AUX7、AUX8映射到电机或其他关键飞行控制的机架。
:::

## 其他外围设备

可选/较少见组件的接线和配置详见各[外围设备](../peripherals/index.md)主题。

## 引脚分配

[Durandal > 引脚分配](../flight_controller/durandal.md#pinouts)

<a id="configuration"></a>## PX4 配置

首先需要通过 _QGroundControl_ 将 [PX4 "Master" 固件](../config/firmware.md#custom) 安装到控制器中。

::: info
Durandal 支持将在 PX4 v1.10 之后的 _stable_ PX4 版本中提供。
:::

更多通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 专用配置请参考：[QuadPlane 垂直起降配置](../config_vtol/vtol_quad_configuration.md)

## 进一步信息

- [Durandal 概述](../flight_controller/durandal.md)
- [Durandal 技术参数表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Durandal_technical_data_sheet_90f8875d-8035-4632-a936-a0d178062077.pdf) (Holybro)
- [Durandal 引脚分配](https://holybro.com/collections/autopilot-flight-controllers/products/Durandal-Pinouts) (Holybro)
- [Durandal_MB_H743sch.pdf](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/durandal/Durandal_MB_H743sch.pdf) (Durandal原理图)
- [STM32H743IIK_pinout.pdf](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/durandal/STM32H743IIK_pinout.pdf) (Durandal Pinmap)