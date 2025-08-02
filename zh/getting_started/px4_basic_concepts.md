

# 基本概念

本主题为无人机及其使用PX4提供了基础介绍（主要面向新手用户，但也适合有经验的用户）。

如果您已经熟悉基本概念，可以继续阅读 [基础组装](../assembly/index.md) 以学习如何连接您的具体飞控硬件。要加载固件并使用 _QGroundControl_ 设置机体，请参阅 [基础配置](../config/index.md)。

## 什么是无人机？

无人机（Unmanned Vehicles，UV）是一种无人操作的“机器人”装置，可以通过手动或自主方式控制。它们可以在空中、地面、水面/水下运行，广泛应用于[消费、工业、政府和军事领域](https://px4.io/ecosystem/commercial-systems/)，包括航拍摄影/录像、货物运输、竞速、搜索与测绘等。

无人机更正式的名称包括：无人飞行器（Unmanned Aerial Vehicles，UAV）、无人地面机体（Unmanned Ground Vehicles，UGV）、无人水面机体（Unmanned Surface Vehicles，USV）、无人水下机体（Unmanned Underwater Vehicles，UUV）。

::: info
术语“无人飞行器系统”（Unmanned Aerial System，UAS）通常指UAV及完整系统中的其他所有组件，包括地面控制站和/或遥控器，以及任何用于控制无人机、捕获和处理数据的系统。
:::

## 无人机类型

有许多不同的机体框架（类型），每种类型又有许多变体。
部分类型及其最适合的使用场景如下所示。

- [多旋翼](../frames_multicopter/index.md) — 多旋翼提供精准悬停和垂直起降功能，但飞行时间较短且速度较慢。
  它们是最受欢迎的飞行器类型，部分原因是组装简单，且PX4的飞行模式使其易于操控，非常适合作为摄像平台。
- [直升机](../frames_helicopter/index.md) — 直升机与多旋翼有相似优势，但机械结构更复杂且效率更高。
  它们也更难操控。
- [飞机（固定翼）](../frames_plane/index.md) — 固定翼飞行器比多旋翼飞行时间更长、速度更快，因此更适合地面测绘等场景。
  然而它们比多旋翼更难操控和着陆，且不适用于需要悬停或低速飞行的场景（例如测绘垂直结构）。
- [VTOL](../frames_vtol/index.md)（垂直起降）- 混合固定翼/多旋翼飞行器结合了两者的优势：可垂直起降并像多旋翼一样悬停，又能像飞机一样转为前飞模式以扩大覆盖范围。
  VTOL通常比多旋翼和固定翼飞机更昂贵，且更难组装和调试。
  它们有多种类型：倾转旋翼机、尾座机、四旋翼飞机等。
- [飞艇](../frames_airship/index.md)/[气球](../frames_balloon/index.md) — 通常提供高空长航时飞行的轻于空气飞行器，但通常牺牲了对飞行速度和方向的控制能力。
- [地面车](../frames_rover/index.md) — 类似汽车的地面车辆。
  它们易于操控且通常使用有趣。
  它们飞行速度不如大多数飞行器，但能承载更重载荷，且静止时功耗较低。
- **船只** — 水面车辆。
- [潜航器](../frames_sub/index.md) — 水下飞行器。

更多信息请参见：

- [机体类型与配置](../airframes/index.md)
- [机体配置](../config/airframe.md)
- [机体参考手册](../airframes/airframe_reference.md)。

## 自动驾驶仪

无人机的"大脑"称为自动驾驶仪。

它至少包括运行在实时操作系统("RTOS")上的_飞行栈_软件，以及在_飞行控制器_(FC)硬件上运行。
飞行栈提供必要的稳定和安全功能，通常还包括一定程度的飞行员辅助功能以用于手动飞行，并自动执行常见任务（如起飞、着陆和执行预定义任务）。

某些自动驾驶仪还包括通用计算系统，可以提供"高级别"的指令和控制，并支持更高级的网络、计算机视觉和其他功能。
这可能被实现为单独的[伴飞计算机](#offboard-companion-computer)，但未来更可能是一个完全集成的组件。

## PX4 飞行堆栈

[PX4](https://px4.io/) 是运行在 NuttX RTOS 上的功能强大的开源自动驾驶仪 _飞行堆栈_。

PX4 的关键特性包括：

- 支持多种不同的机体框架/类型，包括：[多旋翼](../frames_multicopter/index.md)、[固定翼飞机](../frames_plane/index.md)（飞机）、[垂直起降飞行器(VTOL)](../frames_vtol/index.md)（混合多旋翼/固定翼）、[地面车辆](../frames_rover/index.md) 以及 [水下车辆](../frames_sub/index.md)。
- 为 [飞行控制器](#飞行控制器)、[传感器](#传感器)、[载荷](#载荷) 及其他外设提供了丰富的无人机组件选择。
- 灵活且强大的 [飞行模式](#飞行模式) 与 [安全功能](#safety-settings-failsafe)。
- 与 [伴飞计算机](#offboard-companion-computer) 及 [机器人API](../robotics/index.md)（如 [ROS 2](../ros2/user_guide.md) 和 [MAVSDK](http://mavsdk.mavlink.io)）的深度集成。

PX4 是一个更广泛无人机平台的核心部分，该平台包含 [QGroundControl](#qgc) 地面站、[Pixhawk硬件](https://pixhawk.org/) 以及用于通过 MAVLink 协议集成伴飞计算机、摄像头和其他硬件的 [MAVSDK](http://mavsdk.mavlink.io)。
PX4 由 [Dronecode项目](https://www.dronecode.org/) 提供支持。

## 地面控制站（GCS）

地面控制站（GCS）是基于地面的系统，允许无人机操作员监控并控制无人机及其载荷。
已知可与PX4配合使用的部分产品如下所示。

### QGroundControl {#qgc}

Dronecode 地面站软件名为 [QGroundControl](http://qgroundcontrol.com/) ("QGC")。
该软件可在 Windows、Android、MacOS 或 Linux 硬件上运行，并支持多种屏幕尺寸。
您可以从 [此处](http://qgroundcontrol.com/downloads/)（免费）下载。

![QGC 主界面](../../assets/concepts/qgc_fly_view.png)

QGroundControl 通过遥测无线电（双向数据链）与无人机通信，允许您获取实时飞行和安全信息，并通过点击式界面控制机体、云台和其他载荷。
在支持的硬件上，您也可以使用手柄控制器手动飞行机体。
QGC 还可用于可视化规划、执行和监控自主任务，设置地理围栏等更多功能。

QGroundControl 桌面版也可用于烧录 PX4 固件并在无人机的自动驾驶仪/飞行控制器硬件上配置 PX4。

### Auterion Mission Control (AMC) {#amc}

[Auterion Mission Control](https://auterion.com/product/mission-control/) 是一款功能强大且完整的地面控制站应用程序，其设计更针对_飞行员_的使用优化，而非机体配置。  
虽然该软件设计用于与 Auterion 产品配合使用，但也可用于 "vanilla" PX4。

欲了解更多信息，请参阅：

- [AMC文档](https://docs.auterion.com/vehicle-operation/auterion-mission-control)
- [从Auterion Suite下载](https://suite.auterion.com/)

## 无人机组件与部件

### 飞行控制器

飞行控制器（FC）是运行PX4飞行堆栈固件的硬件设备。它们连接到传感器（PX4通过这些传感器确定自身状态），并连接到用于稳定和移动机体的执行器/电机。

<img src="../../assets/flight_controller/cuav_pixhawk_v6x/pixhawk_v6x.jpg" width="230px" title="CUAV Pixhawk 6X" >

PX4可以在多种不同类型的[飞行控制器硬件](../flight_controller/index.md)上运行，范围从[Pixhawk系列](../flight_controller/pixhawk_series.md)控制器到Linux计算机。这些包括[Pixhawk标准版](../flight_controller/autopilot_pixhawk_standard.md)和[厂商支持版](../flight_controller/autopilot_manufacturer_supported.md)开发板。您应选择适合机体物理限制、预期使用场景和成本的开发板。

更多信息请参见：[飞行控制器选择](flight_controller_selection.md)

### 传感器

PX4 使用传感器来确定机体状态，从而实现机体稳定和自主控制。  
机体状态包括：位置/高度、航向、速度、空速、姿态（方向）、不同轴的旋转速率、电池电量等。

PX4 _最少需要_ [陀螺仪](../sensor/gyroscope.md)、[加速度计](../sensor/accelerometer.md)、[磁力计](../gps_compass/magnetometer.md)（指南针）和[气压计](../sensor/barometer.md)。  
这套最小传感器集已集成在 [Pixhawk Series](../flight_controller/pixhawk_series.md) 飞控中（也可能集成在其他控制器平台中）。

可将附加/外部传感器连接到控制器。  
推荐以下传感器：

- 一个 [GNSS/GPS](../gps_compass/index.md) 或其他全球定位来源，用于启用所有自动模式及部分手动/辅助模式。  
  通常使用组合GNSS和指南针的模块，因为外部指南针比飞控内置指南针更抗电磁干扰。

- [空速传感器](../sensor/airspeed.md) 强烈推荐用于固定翼和VTOL机体。
- [距离传感器（测距仪）](../sensor/rangefinders.md) 强烈推荐用于所有机体类型，因为它们能实现更平稳和稳健的降落，并支持多旋翼的地形跟随功能。
- [光流传感器](../sensor/optical_flow.md) 可与距离传感器配合使用，支持多旋翼和VTOL在GNSS信号受限环境中的导航。

有关传感器的更多信息，请参见：[传感器硬件与设置](../sensor/index.md)。

### 输出：电机、舵机、执行器

PX4 使用 _输出_ 来控制：电机转速（例如通过 [电调](#escs-motors)）、飞行舵面（如副翼和襟翼）、相机快门、降落伞、机械抓取器以及许多其他类型的载荷。

输出可以是 PWM 端口或 DroneCAN 节点（例如 DroneCAN [电机控制器](../dronecan/escs.md)）。下图展示了 [Pixhawk 4](../flight_controller/pixhawk4.md) 和 [Pixhawk 4 mini](../flight_controller/pixhawk4_mini.md) 的 PWM 输出端口。

![Pixhawk 4 输出端口](../../assets/flight_controller/pixhawk4/pixhawk4_main_aux_ports.jpg) ![Pixhawk4 mini MAIN 端口](../../assets/flight_controller/pixhawk4mini/pixhawk4mini_pwm.png)

输出分为 `MAIN` 和 `AUX` 输出，并分别编号（即 `MAINn` 和 `AUXn`，其中 `n` 通常为 1 到 6 或 8）。它们也可能标记为 `IO PWM Out` 和 `FMU PWM OUT`（或类似名称）。

:::warning
飞行控制器可能仅包含 `MAIN` PWM 输出（如 _Pixhawk 4 Mini_），或者在 `MAIN` 或 `AUX` 上仅有 6 个输出。请确保选择的控制器具备足够多的端口/输出以满足 [机体](../airframes/airframe_reference.md) 需求。
:::

您可以通过在 QGroundControl 中将相关功能（"电机 1"）分配到所需输出（"AUX1"）来连接几乎任何输出到任意电机或其他执行器：[执行器配置与测试](../config/actuators.md)。注意每种机体的执行器功能（电机和控制面位置）详见 [机体参考](../airframes/airframe_reference.md)。

**注意：**

- Pixhawk 控制器包含 FMU 板，_可能_ 包含单独的 IO 板。如果包含 IO 板，则 `AUX` 端口直接连接到 FMU，`MAIN` 端口连接到 IO 板。否则 `MAIN` 端口连接到 FMU，且没有 `AUX` 端口。
- FMU 输出端口可以使用 [D-shot](../peripherals/dshot.md) 或 _One-shot_ 协议（以及 PWM），这些协议具有更低延迟特性。这对于竞速机或其他需要高性能的机体非常有用。
- `MAIN` 和 `AUX` 中仅有 6-8 个输出，因为大多数飞行控制器仅提供相应数量的 PWM/Dshot/Oneshot 输出。理论上如果总线支持，可以有更多输出（例如 UAVCAN 总线不限制节点数量）。

### 电调与电机

许多 PX4 无人机使用由飞控通过电子调速器（电调）驱动的无刷电机（电调将飞控发出的信号转换为输送给电机的适当功率）。

关于 PX4 支持的电调/电机信息请参考：

- [电调与电机](../peripherals/esc_motors.md)
- [电调校准](../advanced_config/esc_calibration.md)
- [电调固件与协议概述](https://oscarliang.com/esc-firmware-protocols/) (oscarliang.com)

### 电池/电源

PX4飞行器通常由锂聚合物（LiPo）电池供电。电池通常通过[电源模块](../power_module/index.md)或_Power Management Board_连接到系统，这些组件为飞控和电调（电机）提供独立电源。

有关电池和电池配置的信息，请参见[Battery Estimation Tuning](../config/battery.md)以及[基础组装](../assembly/index.md)中的指南（例如[Pixhawk 4接线快速入门 > 电源](../assembly/quick_start_pixhawk4.md#power)）。

### 手动控制

飞行员可以通过 [无线电控制（RC）系统](../getting_started/rc_transmitter_receiver.md) 或 [操纵杆/游戏手柄](../config/joystick.md) 控制器连接 QGroundControl 来手动控制机体。

![Taranis X9D 发射器](../../assets/hardware/transmitters/frsky_taranis_x9d_transmitter.jpg) <img src="../../assets/peripherals/joystick/micronav.jpg" alt="MicroNav 的照片，一个集成操纵杆的地面控制器" width="400px">

无线电系统使用专用的地面发射器和机体上的接收器发送控制信息。在首次调试/测试新机架设计时，或进行竞速/特技飞行（以及其他对延迟敏感的场景）时，应始终使用此类系统。

操纵杆系统通过 QGroundControl 将"标准"电脑游戏操纵杆的控制信息编码为 MAVLink 消息，并通过（共享的）遥测无线电通道发送到机体。只要遥测通道具有足够高的带宽/低延迟，它们可用于大多数手动飞行场景，如起飞、测绘等。

操纵杆常用于集成 GCS/手动控制系统中，因为相较于单独的无线电系统，集成操纵杆更便宜且更容易实现。对于大多数使用场景，较低的延迟并不重要。它们也非常适合飞行 PX4 仿真器，因为可以直接将操纵杆插入地面控制计算机。

::: info
PX4 的自主飞行模式并不 _需要_ 手动控制系统。
:::

### 安全开关

机体可能包含一个_safety switch_，在机体[解锁](#武装和解除武装)之前必须激活此开关（解锁后电机通电，螺旋桨可旋转）。

此开关几乎总是集成在连接到Pixhawk `GPS1` 端口的[GPS](../gps_compass/index.md)模块中——同时还包括[蜂鸣器](#蜂鸣器)和[UI LED](#LED)。

该开关可能默认被禁用，但具体取决于特定的飞控器和机体配置。
你可以通过参数[CBRK_IO_SAFETY](../advanced_config/parameter_reference.md#CBRK_IO_SAFETY)禁用/启用该开关。

::: info
安全开关是可选的。
许多观点认为，用户永远不应接近带电系统，即使是为了启用/禁用此联锁装置也应如此。
:::

### 蜂鸣器

机体通常配备蜂鸣器，用于提供机体状态和可飞行状态的音频通知（参见 [音调含义](../getting_started/tunes.md)）。

该蜂鸣器几乎总是集成在连接到 Pixhawk `GPS1` 端口的 [GPS](../gps_compass/index.md) 模块中——与 [安全开关](#安全开关) 和 [UI LED](#LED) 一起。你可以通过参数 [CBRK_BUZZER](../advanced_config/parameter_reference.md#CBRK_BUZZER) 禁用通知音调。

### LED

机体应配备一个超亮[UI RGB LED](../getting_started/led_meanings.md#ui-led)，以指示当前的飞行准备状态。

历史上，这被集成在飞控板中。
在较新的飞控器上，这几乎总是集成在与Pixhawk `GPS1`端口连接的[I2C外设](../sensor_bus/i2c_general.md)中的[GPS](../gps_compass/index.md)模块中——以及[安全开关](#安全开关)和[蜂鸣器](#蜂鸣器)。

### 数据/遥测无线电

[数据/遥测无线电](../telemetry/index.md) 可在地面控制站（如_QGroundControl_）与运行PX4的机体之间建立无线MAVLink连接。
这使得在机体飞行过程中可以调整参数、实时检查遥测数据、随时更改任务等。

### 机外控制/伴飞计算机

[伴飞计算机](../companion_computer/index.md)（也称为"任务计算机"或"机外计算机"）是与PX4通信的独立机体计算机，用于提供更高层次的指令和控制功能。

伴飞计算机通常运行Linux系统，因为Linux是更通用的软件开发平台，且允许无人机利用现有的计算机视觉、网络等预开发软件。

飞控器和伴飞计算机可能预先集成在单一主板上以简化硬件开发，或者作为独立设备通过串口线、以太网线或WiFi连接。伴飞计算机通常通过高级机器人API（如[MAVSDK](https://mavsdk.mavlink.io/)或[ROS 2](../ros2/user_guide.md)）与PX4通信。

相关主题包括：

- [伴飞计算机](../companion_computer/index.md)
- [机外模式](../flight_modes/offboard.md) - 从地面站或伴飞计算机进行机外控制的飞行模式
- [机器人API](../robotics/index.md)

### SD卡（可移动存储）

PX4 使用 SD 存储卡存储 [飞行日志](../getting_started/flight_reporting.md)，并且使用 UAVCAN 外设和执行 [任务](../flying/missions.md) 也需要 SD 卡。

默认情况下，如果没有插入 SD 卡，PX4 在启动时会播放两次 [格式失败（2声提示音）](../getting_started/tunes.md#format-failed) 提示音（并且上述所有功能将不可用）。

::: tip
Pixhawk 板最大支持的 SD 卡容量为 32GB。
推荐使用 _SanDisk Extreme U3 32GB_ 和 _Samsung EVO Plus 32_ 存储卡 [高度推荐](../dev_log/logging.md#sd-cards)。
:::

SD 卡仍然是可选配置。
不带 SD 卡插槽的飞控可以：

- 使用参数 [CBRK_BUZZER](../advanced_config/parameter_reference.md#CBRK_BUZZER) 禁用通知提示音。
- [流式传输日志](../dev_log/logging.md#log-streaming) 到其他组件（伴飞设备）。
- 将任务存储在 RAM/FLASH 中。
  <!-- 太底层了，此处不适用。但参见 Intel Aero 中的 FLASH_BASED_DATAMAN: https://github.com/PX4/PX4-Autopilot/blob/main/boards/intel/aerofc-v1/src/board_config.h#L115 -->

## 载荷

载荷是机体携带以满足用户或任务目标的设备，例如测绘任务中的摄像头、检测任务中使用的仪器（如辐射探测器），以及需要投送的货物。PX4支持多种摄像头和广泛的载荷类型。

载荷连接到[飞行控制器输出](#outputs-motors-servos-actuators)，可通过任务自动触发，或通过遥控器/摇杆手动触发，也可通过地面站（使用MAVLink/MAVSDK命令）触发。

欲了解更多详情，请参见：[Payloads & Cameras](../payloads/index.md)

## 武装和解除武装

当所有电机和执行器通电时，机体被称为处于_武装_状态；当所有设备断电时被称为_解除武装_状态。  
还有一种_预武装_状态，此时仅舵机执行器通电，主要用于测试。

机体通常在地面处于解除武装状态，并且必须在当前飞行模式下起飞前进行武装。

:::warning
武装状态的机体存在危险，因为螺旋桨可能在没有进一步用户输入的情况下随时开始旋转，且在许多情况下会立即开始旋转。
:::

武装和解除武装操作默认通过遥控器摇杆的_手势_触发。  
在Mode 2发射器上，通过将油门/偏航摇杆保持在_右下角_一秒来武装机体；通过将摇杆保持在左下角一秒来解除武装。  
也可以配置PX4通过遥控器开关或按钮进行武装（同样可以通过地面站发送MAVLink武装指令）。

为减少意外发生，机体在地面时应尽可能少进行武装操作。默认情况下：

- 不使用时机体处于_解除武装_或_预武装_（电机断电）状态，起飞前必须显式_武装_。
- 如果机体在武装后未及时起飞，将自动解除武装/预武装（解除武装时间可配置）。
- 着陆后短时间内将自动解除武装/预武装（时间可配置）。
- 如果机体状态不健康，则禁止武装。
- 如果机体配备有[安全开关](#安全开关)且未激活，则禁止武装。
- 如果VTOL机体处于固定翼模式，则禁止武装([默认设置](../advanced_config/parameter_reference.md#CBRK_VTOLARMING))。
- 由于其他可选的[武装前提条件设置](../config/safety.md#arming-pre-conditions)（如低电量），可能禁止武装。

在预武装状态下仍可使用执行器，而解除武装时所有设备断电。  
预武装和解除武装状态都应是安全的，特定机体可能支持其中一种或两种状态。

:::tip
有时机体无法武装，原因可能不明显。  
QGC v4.2.0（写作时的每日构建版本）及更高版本在[飞行视图 > 武装和预飞检查](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/fly_view/fly_view.html#arm)中提供武装检查报告。  
从PX4 v1.14开始，该报告提供全面的武装问题信息及可能的解决方案。
:::

关于武装和解除武装配置的详细概述可参考以下内容：[预武装、武装、解除武装配置](../advanced_config/prearm_arm_disarm.md)。

## 飞行模式

飞行模式是特殊的操作状态，为用户提供不同类型的机体自动化和自动驾驶仪辅助功能。

_自主模式_ 完全由自动驾驶仪控制，不需要飞行员/遥控器输入。  
例如，用于自动化常见任务（如起飞、返回航点和降落）。  
其他自主模式可执行预设任务、跟随GPS信标，或接收来自外部计算机或地面站的指令。

_手动模式_ 由用户通过遥控器操纵杆/摇杆控制，并获得自动驾驶仪的辅助。  
不同的手动模式会启用不同的飞行特性——例如，某些模式允许特技动作，而其他模式则无法翻转，并能在逆风中保持位置/航向。

:::tip
并非所有模式都适用于所有机体类型，某些模式的使用需要满足特定条件（例如，许多模式需要全局位置估计）。
:::

PX4为每种机体实现的飞行模式概述如下：

- [飞行模式（多旋翼）](../flight_modes_mc/index.md)
- [飞行模式（固定翼）](../flight_modes_fw/index.md)
- [飞行模式（垂直起降）](../flight_modes_vtol/index.md)
- [驾驶模式（地面车）](../flight_modes_rover/index.md)

关于如何设置遥控器开关以启用不同飞行模式的说明，请参见[飞行模式配置](../config/flight_mode.md)。

PX4还支持通过[ROS 2](../ros2/index.md)实现的外部模式，使用[PX4 ROS 2 Control Interface](../ros2/px4_ros2_control_interface.md)。  
这些模式与PX4内部模式无法区分，可用于覆盖内部模式以实现更高级功能，或创建全新功能。  
请注意，这些功能依赖于ROS 2，因此只能在配备[外部计算机](#offboard-companion-computer)的系统上运行。

## 安全设置（安全措施）

PX4 具有可配置的安全措施系统，能够在出现问题时保护并恢复您的机体！  
这些系统允许您指定安全飞行的区域和条件，以及触发安全措施时执行的操作（例如，降落、悬停或返回指定位置）。

::: info  
您只能为第一个安全措施事件指定操作。  
一旦发生安全措施，系统将进入特殊处理代码，后续的安全措施触发将由独立的系统级和机体特定代码处理。  
:::  

主要的安全措施区域如下：  

- 电池电量低  
- 遥控器（RC）丢失  
- 位置丢失（全局位置估计质量过低）。  
- 机外模式丢失（例如，与协处理器失去连接）  
- 数据链丢失（例如，与地面站（GCS）的遥测连接丢失）。  
- 地理围栏违规（限制机体在虚拟圆柱体内飞行）。  
- 任务安全措施（防止在新的起飞位置运行先前任务）。  
- 交通避让（由应答机数据触发，例如 ADSB 应答机）。  

如需更多信息，请参阅：[Safety](../config/safety.md)（基础配置）。

## 朝向与方向

所有机体（包括船只和飞行器）都有基于其前向运动的朝向或姿态。

![Frame Heading](../../assets/concepts/frame_heading.png)

::: info
对于垂直起降尾座式飞行器（VTOL Tailsitter），其朝向是相对于多旋翼配置而言的（即机体在起飞、悬停和着陆时的姿态）。
:::

了解机体的朝向方向对于将自动驾驶仪与机体运动向量对齐非常重要。  
多旋翼飞行器即使在各个方向对称时也有朝向！  
通常制造商使用彩色螺旋桨或彩色臂来指示朝向。

![Frame Heading TOP](../../assets/concepts/frame_heading_top.png)

在我们的示意图中，将通过红色螺旋桨标识多旋翼飞行器的朝向。

您可以在[飞行控制器朝向](../config/flight_controller_orientation.md)中深入了解有关朝向的更多信息。