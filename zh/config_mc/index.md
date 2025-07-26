# 多旋翼配置

多旋翼的配置和校准流程与其他机型保持一致，主要包括以下步骤：固件选择、机体配置（包括执行器/电机几何结构和输出映射）、传感器配置和校准、安全及其他功能配置，最后进行调参。

本主题通过选取以下文档中的相关主题进行讲解：[标准配置](../config/index.md)、[高级配置](../advanced_config/index.md) 和 [飞控外设](../peripherals/index.md)，并结合多旋翼特有的调参主题。

::: info
本主题是首次配置和校准新多旋翼机体时的推荐入门指南。
:::

## 加载固件

第一步是将[加载 PX4 固件](../config/firmware.md)到您的[飞控](../flight_controller/index.md)中。  
最简单的方法是使用 QGroundControl，它会自动为您特定的飞控硬件选择合适的固件。  
默认情况下 QGC 会安装最新稳定版的 PX4，如果需要也可以选择测试版或自定义版本。

相关主题：

- [加载固件](../config/firmware.md)

## 机体选择与配置

本节解释如何配置机体类型（多旋翼）、特定的电机/飞行控制几何形状以及电机输出。

首先[选择多旋翼机体框架](../config/airframe.md)（选项在[机体框架参考 > 多旋翼](../airframes/airframe_reference.md#copter)中列出）。如果你的机体品牌和型号有对应的框架，应选择匹配的框架；如果没有，则选择最接近你机体几何形状的"Generic"（通用）框架类型，主要根据电机数量和相对位置进行匹配。例如，对于[Quadrotor X](../airframes/airframe_reference.md#quadrotor-x)框架，你需要在列表中查找对应名称，若不存在则选择[通用四旋翼X](../airframes/airframe_reference.md#copter_quadrotor_x_generic_quadcopter)框架。

::: info
任何选定的多旋翼框架都可以在下一步（执行器配置）中进行修改，添加/删除电机并调整几何形状，以及指定飞行控制器输出连接到特定电机及其输出属性。选择匹配机体的框架可减少配置工作量。

:::details 工作原理（详细说明）
选择机体框架会应用一个[框架配置文件](../dev_airframes/adding_a_new_frame.md#adding-a-frame-configuration)，其中包含预定义的[参数](../advanced_config/parameters.md)，例如[CA_AIRFRAME=0](../advanced_config/parameter_reference.md#CA_AIRFRAME)用于机体类型，[CA_ROTOR_COUNT](../advanced_config/parameter_reference.md#CA_ROTOR_COUNT)用于旋翼数量。

框架配置可以定义机体的所有信息，从几何形状和输出映射到调校和校准值。不过，当你首次启动新机体时，框架通常只包含基本配置：

- 命名为"Generic"的框架会定义机体类型、旋翼数量和"占位符"旋翼位置。选择框架后，你需要定义实际几何形状并配置输出。
- 命名为型号/品牌的框架会定义机体类型、旋翼数量、实际旋翼位置和电机方向。选择框架后通常仍需配置输出。
:::

下一步是定义机体的[几何形状](../config/actuators.md#motor-geometry-multicopter)（电机数量及其相对位置），并[将这些电机](../config/actuators.md#actuator-outputs)分配到飞行控制器上实际连接的物理输出（这两项内容在[执行器配置与测试](../config/actuators.md)中涵盖）。

如果使用PWM ESC和OneShot ESC（不包括DShot和DroneCAN/Cyphal ESC），则在进行[电机配置](../config/actuators.md#motor-configuration)前应执行[ESC校准](../advanced_config/esc_calibration.md)。这确保所有ESC对相同输入提供相同的输出（理想情况下应先校准ESC，但校准前需要先完成输出映射）。

最后一步是[电机配置](../config/actuators.md#motor-configuration)：

- [反转任何不匹配几何配置中旋转方向的电机](../config/actuators.md#reversing-motors)。对于DShot ESC，可以通过Acuator Testing UI完成此操作。
- 对于PWM、OneShot和CAN ESC，设置电机在非激活、低速和高速状态下的输入限值（DShot ESC不需要此操作）

相关主题：

- [机体（框架）选择](../config/airframe.md) — 根据你的框架选择机体类型。
- [执行器配置与测试](../config/actuators.md) — 机体几何形状、输出映射、电机配置和测试。
- [ESC校准](../advanced_config/esc_calibration.md) — 在输出映射和电机配置（上文主题）之间进行，适用于PWM和OneShot ESC。

## 传感器设置与校准

PX4 最常依赖磁力计（指南针）提供方向信息，气压计提供高度，陀螺仪测量机体速率，加速度计测量姿态，并通过 GPS/GNSS 获取全局位置。Pixhawk 飞行控制器（及其他许多控制器）内置磁力计、加速度计、陀螺仪和气压计。内置指南针通常可靠性较低，因此常见做法是外接指南针（通常与 GNSS 接收器集成在同一设备中）。

首先需要设置 [传感器方向](../config/flight_controller_orientation.md)，告知 PX4 自动驾驶仪（及其内置传感器）和外接指南针相对于机体的朝向。通常只需将控制器朝向机体前方，无需额外设置。完成方向设置后，需要对指南针、陀螺仪和加速度计进行校准。

核心传感器设置详见以下主题：

- [传感器方向](../config/flight_controller_orientation.md)
- [指南针](../config/compass.md)
- [陀螺仪](../config/gyroscope.md)
- [加速度计](../config/accelerometer.md)

PX4 可使用其他外设，例如距离传感器、光流传感器、避障警报器、摄像头等：

- [飞行控制器外设](../peripherals/index.md) - 设置特定传感器、可选传感器、执行器等。

::: info
无需校准/配置的传感器包括：

- [水平仪](../config/level_horizon_calibration.md)校准：若已将飞行控制器安装为水平状态，通常无需校准。
- 不存在的传感器，或未被 PX4 多旋翼用于飞行控制的传感器，例如 [空速传感器](../config/airspeed.md)。
- 无需校准的传感器，包括：气压计和 GNSS。

:::

## 手动控制设置

飞行员可通过无线电控制（RC）系统或通过QGroundControl连接的操纵杆/游戏手柄控制器手动控制机体。

::: info
手动控制对于安全启动新机体至关重要！
:::

无线电控制：

- [无线电控制器（RC）设置](../config/radio.md)
- [飞行模式配置](../config/flight_mode.md)

操纵杆/游戏手柄：

- [操纵杆设置](../config/joystick.md)（包括按钮/飞行模式映射）

## 安全配置

PX4 可以配置为自动处理低电量、失去遥控或数据链、飞离家点太远等条件：

- [电池估算调优](../config/battery.md) — 估算剩余电量（用于低电量应急措施）
- [安全配置（应急措施）](../config/safety.md)

## 调校

调校是最后一步，需在完成大部分其他设置和配置后进行。

- 速率和姿态控制器：
- [Autotune](../config/autotune_mc.md) — 自动调校PX4的速率和姿态控制器（推荐）。

  ::: info
  自动调校适用于在所有机体轴上具有合理控制权限和动态特性的机架。
  已主要在竞速四旋翼和X500上测试，预期在可倾斜旋翼的三旋翼上效果较差。
  
  如果自动调校出现问题，需要按照这些指南进行手动调校：
  
  - [MC PID调校（手动/基础）](../config_mc/pid_tuning_guide_multicopter_basic.md) — 手动调校基础指南。
  - [MC PID调校指南（手动/详细）](../config_mc/pid_tuning_guide_multicopter.md) — 带详细说明的手动调校。
  
  :::

- [MC滤波/控制延迟调校](../config_mc/filter_tuning.md) — 控制延迟与噪声滤波的平衡。
- [MC设定点调校（轨迹生成器）](../config_mc/mc_trajectory_tuning.md)
  - [MC加加速度限制轨迹类型](../config_mc/mc_jerk_limited_type_trajectory.md)
- [多旋翼竞速机设置](../config_mc/racer_setup.md)

<!-- 
- 说明PX4中需要调校的内容、可调校的内容及每个主题的覆盖范围
- 我认为我们应该先列出所有可能的调校内容（如位置调校等）- 有这样的列表吗？
 -->
<!-- TBD 这只是待整理的文本

据我所知，Autotune已在多种非定制平台（如X500、竞速四旋翼、Loong标准垂直起降机）上测试。我仅在三旋翼上使用过一次，roll和pitch调校成功但yaw调校不稳定，后来虽有改进但尚未合并：https://github.com/PX4/PX4-Autopilot/pull/21857
Autotune从未在直升机上测试过。
理论上能否在任意电机数量的机架上使用Autotune？
理论上可行但需要所有轴上具有合理控制权限，因此预期在无swashplate的单旋翼机上效果不佳，控制器也可能无法开箱即用。之前在可倾斜旋翼设计（如三旋翼、双旋翼）上遇到过问题...
  
PX4能否理解如何自动调校？
对于所有轴上具有合理权限和动态特性的机体，Autotune应该有效。可倾斜电机（如三旋翼）的动态特性最少，因此测试较少。
我认为混合系统能处理任何几何结构...
是的但必须物理可行。例如四旋翼所有电机同向旋转，PX4会"处理"但需要特定控制器才能工作，单旋翼机或无倾斜电机的三旋翼同理。
-->

## 参见

- [QGroundControl > Setup](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/setup_view.html)
- [飞控外设](../peripherals/index.md) - 配置特定传感器、可选传感器、执行器等
- [高级配置](../advanced_config/index.md) - 出厂/原厂校准、配置高级功能、较少见的配置
- 机体相关配置/调参：
  - **多旋翼配置/调参**
  - [直升机配置/调参](../config_heli/index.md)
  - [固定翼配置/调参](../config_fw/index.md)
  - [垂直起降配置/调参](../config_vtol/index.md)