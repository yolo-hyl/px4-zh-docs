# Cube接线快速入门

:::warning
PX4不生产此（或任何）飞控系统。
请联系[制造商](https://cubepilot.org/#/home)获取硬件支持或合规性问题解答。

请注意，尽管[Cube Black](../flight_controller/pixhawk-2.md)被PX4[完全支持](../flight_controller/autopilot_pixhawk_standard.md)，[Cube Yellow](../flight_controller/cubepilot_cube_yellow.md)和[Cube Orange](../flight_controller/cubepilot_cube_orange.md)属于[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)设备。
:::

本快速入门指南介绍了如何为带商标的_Cube_<sup>&reg;</sup>飞控系统供电并连接其最重要的外设。

<img src="../../assets/flight_controller/cube/orange/cube_orange_hero.jpg" width="350px" /> <img src="../../assets/flight_controller/cube/cube_black_hero.png" width="350px" /> <img src="../../assets/flight_controller/cube/yellow/cube_yellow_hero.jpg" width="150px" />

:::tip
这些操作说明适用于所有Cube版本，包括[Cube Black](../flight_controller/pixhawk-2.md)、[Cube Yellow](../flight_controller/cubepilot_cube_yellow.md)和[Cube Orange](../flight_controller/cubepilot_cube_orange.md)。
更多/更新的信息可参考[Cube用户手册](https://docs.cubepilot.org/user-guides/autopilot/the-cube-user-manual) (Cube Docs)。
:::

## 配件

Cube 通常包含您在 [购买](../flight_controller/pixhawk-2.md#stores) 时所需的大多数（或全部）配件。

![Cube 配件](../../assets/flight_controller/cube/cube_accessories.jpg)

例外情况是某些套件不包含 GPS，需要单独购买 ([详见下文](#gps))。

## 接线概述

下图展示了如何连接最重要的传感器和外设。接下来的章节将详细说明这些内容。

![Cube - 接线概述](../../assets/flight_controller/cube/cube_wiring_overview.jpg)

1. [遥测系统](#telemetry) — 允许您规划/执行任务，并实时控制和监控机体。通常包括遥测电台、平板/PC和地面站软件。
2. [蜂鸣器](#buzzer) — 提供指示无人机状态的音频信号
3. [遥控接收系统](#rc_control) — 连接到操作员可用来手动飞行机体的手持遥控器（图中展示的是带有PWM->PPM转换器的PWM接收器）
4. （专用）[安全开关](#safety-switch) — 按住以锁定/解锁电机。如果您没有使用推荐的内置安全开关的[GPS](#gps)，则必须使用此开关
5. [GPS、指南针、LED、安全开关](#gps) — 推荐的GPS模块集成了GPS、指南针、LED和安全开关
6. [供电系统](#power) — 为Cube和电机电调供电。包括锂聚合物电池、电源模块和可选的电池报警系统（当电池电量低于预设值时发出音频警告）

::: info
标记为 `GPS2` 的端口在 PX4 中映射到 `TEL4`（即如果连接到标记为 `GPS2` 的端口，请将连接硬件的[串口配置参数](../peripherals/serial_configuration.md)分配为 `TEL4`）。
:::

:::tip
更多关于可用端口的信息请见：[Cube > 端口](../flight_controller/pixhawk-2.md#ports)
:::

## 安装和调整控制器

将Cube尽可能靠近机体重心安装，理想状态下应保持顶面朝上，箭头指向机体前方（注意Cube顶部的_微妙_箭头标记）

![Cube安装 - 前方方向](../../assets/flight_controller/cube/cube_mount_front.jpg)

::: info
如果控制器无法以推荐/默认方向安装（例如因空间限制），您需要在自动驾驶仪软件中配置实际使用的方向：[飞行控制器方向](../config/flight_controller_orientation.md)
:::

Cube可通过减震泡沫垫（套件内含）或安装螺丝进行安装。Cube配件中的安装螺丝适用于1.8mm厚度的框架板。定制螺丝应为M2.5，螺纹长度在Cube内范围6mm~7.55mm。

![Cube安装 - 安装板](../../assets/flight_controller/cube/cube_mount_plate_screws.jpg)

<a id="gps"></a>## GPS + 电子罗盘 + 安全开关 + LED

推荐的GPS模块是_Here_和[Here+](../gps_compass/rtk_gps_hex_hereplus.md)，这两个模块都集成了GPS模块、电子罗盘、安全开关和[LED](../getting_started/led_meanings.md)。
模块的主要区别在于_Here+_支持通过[RTK](../gps_compass/rtk_gps.md)实现厘米级定位。其他方面它们的使用和连接方式相同。

:::warning
[Here+](../gps_compass/rtk_gps_hex_hereplus.md) 已被 [Here3](https://www.cubepilot.org/#/here/here3) 取代，后者是一个集成电子罗盘和[LED](../getting_started/led_meanings.md)（但无安全开关）的[DroneCAN](../dronecan/index.md) RTK-GNSS模块。
请参见 [DroneCAN](../dronecan/index.md) 了解 _Here3_ 的接线和PX4配置信息。
:::

模块应安装在机体上尽可能远离其他电子设备的位置，方向标记应指向机体前方（将电子罗盘与电子设备分离可减少干扰）。必须使用附带的8针电缆连接到`GPS1`端口。

下图显示了模块及其连接的示意图。

![Here+ 连接器示意图](../../assets/flight_controller/cube/here_plus_connector.png)

::: info
GPS模块集成的安全开关默认处于启用状态（启用后，PX4将不允许你启动机体）。
要禁用安全开关，请按住安全开关1秒。再次按下安全开关可启用安全功能并解除机体启动（如果由于某种原因无法通过遥控器或地面站解除启动，此功能会很有用）。
:::

:::tip
如果要使用传统的6针GPS模块，套件附带了一根可用于连接GPS和[安全开关](#safety-switch)的线缆。
:::

## 安全开关

Cube 附带的 _专用_ 安全开关仅在未使用推荐的 [全球定位系统](#gps)（其内置安全开关）时才需要。

如果未使用 GPS 进行飞行，必须将开关直接连接到 `GPS1` 接口以解锁机体并飞行（或通过配套电缆连接，如果使用老式六针 GPS 的话）。

## 蜂鸣器

蜂鸣器会播放[音调和旋律](../getting_started/tunes.md)，用于通过声音提示机体状态（包括有助于调试启动问题的提示音，以及通知可能影响机体安全运行的条件的提示音）。

蜂鸣器应按照下图连接到USB端口（无需进一步配置）。

![Cube Buzzer](../../assets/flight_controller/cube/cube_buzzer.jpg)

<a id="rc_control"></a>## 遥控

如果你希望_手动_控制机体(PX4在自主飞行模式下不需要遥控系统)，则需要[遥控器(RC)无线电系统](../getting_started/rc_transmitter_receiver.md)。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后_绑定_它们以实现通信(阅读随附的特定发射机/接收机说明)。

以下说明展示了如何连接不同类型的接收机。

### PPM-SUM / Futaba S.Bus 接收机

使用提供的三线舵机线，将地线(-)、电源(+)和信号(S)线连接到RC引脚。

![Cube - RCIN](../../assets/flight_controller/cube/cube_rc_in.jpg)

### Spektrum 卫星接收机

Spektrum DSM、DSM2 和 DSM-X 卫星遥控接收机连接到 **SPKT/DSM** 端口。

![Cube - Spektrum](../../assets/flight_controller/cube/cube_rc_spektrum.jpg)

### PWM 接收机

Cube 无法直接连接到具有_每个通道独立线_的 PPM 或 PWM 接收机。
因此 PWM 接收机必须通过 **RCIN** 端口_经_ PPM 编码模块连接，
该模块可从 hex.aero 或 proficnc.com 购买。

## 电源

Cube 通常通过随套件提供的电源模块（Power Module）从锂离子聚合物（LiPo）电池供电，该模块连接到 **POWER1** 接口。  
电源模块为飞控板提供可靠的电源及电压/电流指示，并可**单独**为多旋翼机体驱动电机的电调（ESC）供电。

多旋翼机体的典型电源配置如下所示。

![Power Setup - MC](../../assets/flight_controller/cube/cube_wiring_power_mc.jpg)

::: info
**MAIN/AUX** 的电源（+）总线**未通过**电源模块为飞控供电。  
为了驱动舵机（如方向舵、升降舵等），需要单独供电。

这可以通过将电源总线连接到带 BEC 的电调、独立 5V BEC 或 2S LiPo 电池实现。  
请确保所使用舵机的电压范围合适！
:::

<a id="telemetry"></a>## 遥测系统（可选）

遥测系统允许您通过地面站与机体进行通信、监控和飞行控制（例如，您可以将无人机引导至特定位置，或上传新的任务）。

通信通道通过 [Telemetry Radios](../telemetry/index.md) 实现。机体端无线电应连接到 **TELEM1** 端口（若连接到该端口则无需进一步配置）。另一端无线电连接到您的地面站电脑或移动设备（通常通过 USB 连接）。

![遥测无线电](../../assets/flight_controller/cube/cube_schematic_telemetry.jpg)

## SD卡（可选）

SD卡强烈推荐使用，因为它们用于[记录和分析飞行细节](../getting_started/flight_reporting.md)、运行任务，以及使用UAVCAN-bus硬件。
将Micro-SD卡插入Cube（如尚未插入）。

![Cube - 安装SD卡](../../assets/flight_controller/cube/cube_sdcard.jpg)

:::tip
更多信息请参见 [基础概念 > SD卡（可移除存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。
:::

## 电机

电机/舵机按照机体在[机体参考](../airframes/airframe_reference.md)中指定的顺序连接到**MAIN**和**AUX**端口。

![Cube - 电机连接](../../assets/flight_controller/cube/cube_main_aux_outputs.jpg)

::: info
此参考列出了所有支持的空中和地面机体结构的输出端口到电机/舵机的映射（如果您的机体结构未在参考中列出，则使用对应类型的"generic"机体结构）。
:::

:::warning
不同机体结构的映射不一致（例如您不能依赖油门始终在相同输出上）。请确保使用适合您机体的正确映射。
:::

## 其他外设

可选或不常用组件的接线和配置包含在各个外设的[主题](../peripherals/index.md)中。

::: info
如果将外设连接到标记为 `GPS2` 的端口，请将硬件的 PX4 [串口配置参数](../peripherals/serial_configuration.md) 设置为 `TEL4`（而非 GPS2）。
:::

## 配置

配置通过 [QGroundControl](http://qgroundcontrol.com/) 完成。

下载安装并运行 _QGroundControl_ 后，按如下方式连接开发板到电脑。

![Cube - USB 连接到电脑](../../assets/flight_controller/cube/cube_usb_connection.jpg)

基本/通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

四旋翼混合机特定配置请参考：[四旋翼混合机 VTOL 配置](../config_vtol/vtol_quad_configuration.md)

<!-- 其他 VTOL 类型和固定翼的配置说明是什么？上述内容是否适用于尾坐式等机型？ -->

### 引导程序更新

如果在烧录 PX4 固件后收到 [Program PX4IO(../getting_started/tunes.md#program-px4io) 警告音]，可能需要更新引导程序。

安全开关可用于强制更新引导程序。  
使用该功能时，请先断开 Cube 电源，按住安全开关，然后通过 USB 为 Cube 供电。

## 进一步信息

- [Cube Black](../flight_controller/pixhawk-2.md)
- [Cube Yellow](../flight_controller/cubepilot_cube_yellow.md)
- [Cube Orange](../flight_controller/cubepilot_cube_orange.md)
- Cube Docs (制造商)：
  - [Cube模块概述](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)
  - [Cube用户手册](https://docs.cubepilot.org/user-guides/autopilot/the-cube-user-manual)
  - [Mini载板](https://docs.cubepilot.org/user-guides/carrier-boards/mini-carrier-board)