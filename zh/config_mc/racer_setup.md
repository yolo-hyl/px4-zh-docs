# 多旋翼竞速机设置

本页面介绍如何设置和配置多旋翼竞速机以实现最佳性能（特别是针对[特技模式](../flight_modes_mc/acro.md)）。

请注意，竞速机属于高速机体，专门设计为动力过剩！
你应已具备一定经验，或请有经验者协助操作。

::: tip
此处描述的许多内容也可用于提升其他类型多旋翼飞行器的飞行性能。
:::

::: info
竞速机通常不配备某些传感器（例如GPS）。
因此，可用的故障安全选项更少。
:::

## 构建选项

竞速无人机通常会省略部分传感器。

最小配置是仅使用陀螺仪和加速度计传感器。

::: info
如果板载有内置磁力计，不应使用（小型竞速机特别容易受到强电磁干扰）。
:::

竞速无人机通常不配备GPS，因为会增加重量且在碰撞时容易损坏（GPS+外置磁力计必须安装在远离高电流的GPS支架上以避免磁干扰，不幸的是这也意味着容易损坏）。

不过添加GPS也有一些优势，尤其对新手来说：

- 可以进入位置保持模式，机体将保持在原位。
  如果丢失方向或需要减速时非常有用。
  也可以用于安全降落。
- 可使用[Return模式](../flight_modes_mc/return.md)，既可通过开关切换，也可作为遥控丢失/低电量的保护模式。
- 在坠毁时可以获取最后位置。
- 日志中包含飞行轨迹，意味着可以以3D形式回顾飞行。
  这有助于提升特技飞行技巧。

::: info
在激烈特技动作中，GPS可能会短暂丢失定位。
如果在此期间切换到[position模式](../flight_modes_mc/position.md)，将切换为[altitude模式](../flight_modes_mc/altitude.md)，直到定位恢复。
:::

## 硬件设置

以下段落描述了构建机体时的几个关键点。  
如果需要完整的构建说明，可以参考 [QAV-R 5" KISS ESC Racer](../frames_multicopter/qav_r_5_kiss_esc_racer.md) 构建日志。

### 振动设置

有多种安装方法可以减少振动。  
例如，飞控可以使用减震泡沫安装，或通过 [O-rings](../frames_multicopter/qav_r_5_kiss_esc_racer.md#mounting) 安装。

虽然没有单一最佳方法，但通常使用高质量组件（如框架、电机、螺旋桨）可以显著减少振动问题，例如 [QAV-R 5" KISS ESC Racer](../frames_multicopter/qav_r_5_kiss_esc_racer.md) 中使用的组件。

请务必使用 **平衡螺旋桨**。

### 重心

确保重心尽可能接近推力中心。  
左右平衡通常不是问题，但前后平衡可能会存在问题。  
可以通过移动电池至正确位置，并在框架上做标记以确保每次安装位置一致。

::: info
积分项可以补偿不平衡的设置，而自定义混控器可以做得更好。  
但最佳实践是通过机体设置直接修正任何不平衡。
:::

## 软件设置

完成赛车的组装后，需要进行软件配置。

按照[基础配置指南](../config/index.md)进行操作。  
特别注意选择与您的机架最匹配的[Airframe](../config/airframe.md)（通常选择[通用250赛车](../airframes/airframe_reference.md#copter_quadrotor_x_generic_250_racer)机架，该配置默认设置了一些赛车特有参数）。

这些参数非常重要：

- 通过选择输出组的协议启用One-Shot或DShot：[执行器配置](../config/actuators.md)。  
- 设置稳定模式下的最大滚转/俯仰/偏航速率：[MC_ROLLRATE_MAX](../advanced_config/parameter_reference.md#MC_ROLLRATE_MAX)、[MC_PITCHRATE_MAX](../advanced_config/parameter_reference.md#MC_PITCHRATE_MAX) 和 [MC_YAWRATE_MAX](../advanced_config/parameter_reference.md#MC_YAWRATE_MAX)。  
  最大倾斜角度通过 [MPC_MAN_TILT_MAX](../advanced_config/parameter_reference.md#MPC_MAN_TILT_MAX) 配置。  
- 最小推力 [MPC_MANTHR_MIN](../advanced_config/parameter_reference.md#MPC_MANTHR_MIN) 应设置为 0。

### 估计器

如果您使用GPS，可以跳过本节并使用默认估计器。  
否则应切换到Q姿态估计器，该估计器无需磁力计或气压计即可工作。

启用方法：设置 [ATT_EN = 1](../advanced_config/parameter_reference.md#ATT_EN)、[EKF2_EN =0 ](../advanced_config/parameter_reference.md#EKF2_EN) 和 [LPE_EN = 0](../advanced_config/parameter_reference.md#LPE_EN)（更多信息请参见[切换状态估计器](../advanced/switching_state_estimators.md#how-to-enable-different-estimators)）。

然后修改以下参数：

- 如果系统没有磁力计，将 [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG) 设置为 `0`。  
- 如果系统没有气压计，将 [SYS_HAS_BARO](../advanced_config/parameter_reference.md#SYS_HAS_BARO) 设置为 `0`。  
- 配置Q估计器：将 [ATT_ACC_COMP](../advanced_config/parameter_reference.md#ATT_ACC_COMP) 设置为 `0`，[ATT_W_ACC](../advanced_config/parameter_reference.md#ATT_W_ACC) 设置为 0.4，[ATT_W_GYRO_BIAS](../advanced_config/parameter_reference.md#ATT_W_GYRO_BIAS) 设置为 0。  
  如果需要，后续可以调整这些参数。

### 失控保护

配置[遥控丢失和低电量失控保护](../config/safety.md)。  
如果您未使用GPS，应将失控保护设置为 **Lockdown**（关闭电机）。  
通过关闭遥控器进行台面测试（机体处于武装状态时）。

确保分配[杀伤开关](../config/safety.md#kill-switch)或[武装开关](../config/safety.md#arm-disarm-switch)。  
测试并训练使用！

### PID调校

::: info  
在进行任何调校前，请务必校准电调。  
:::

此时应准备好首次试飞。

假设机体使用默认设置即可飞行，接下来进行[基础多旋翼PID调校](../config_mc/pid_tuning_guide_multicopter_basic.md)。  
机体需要处于 **欠调** 状态（**P**和**D**增益应设置过低），以避免控制器产生可能被误认为噪声的振荡（默认增益可能已足够）。  
这对于[滤波器调校](#filter-tuning)至关重要（后续还会有第二轮PID调校）。

### 控制延迟

控制延迟是从机体物理扰动到电机响应变化的延迟时间。

:::tip  
尽可能减少控制延迟是 **至关重要的**！  
较低的延迟允许您增加速率 **P** 增益，从而提升飞行性能。  
即使增加1毫秒的延迟也会产生显著影响。  
:::

影响延迟的因素包括：

- 柔软的机架或减震装置会增加延迟（它们起到滤波作用）。  
- 软件中的[低通滤波器](../config_mc/filter_tuning.md)和传感器芯片上的滤波器在增加延迟的同时提高噪声抑制能力。  
- PX4软件内部：传感器信号需要在驱动器中读取，然后通过控制器传输到输出驱动器。  
- IO芯片（MAIN引脚）相比AUX引脚会增加约5.4毫秒的延迟（此问题不适用于_Pixracer_或_Omnibus F4_，但适用于Pixhawk）。  
  为避免IO延迟，将电机连接到AUX引脚。  
- PWM输出信号：优先启用[Dshot](../peripherals/dshot.md)以降低延迟（或在不支持DShot时使用One-Shot）。  
  协议选择在[执行器配置](../config/actuators.md)期间为输出组指定。

### 滤波器调校

滤波器在控制延迟和噪声抑制之间进行权衡，这两者都会影响性能。  
更多信息请参见：[滤波器/控制延迟调校](../config_mc/filter_tuning.md)

### PID调校（第二轮）

现在进行第二轮PID调校，此时尽可能紧凑，并调整推力曲线。

:::tip  
您可以使用[基础多旋翼PID调校](../config_mc/pid_tuning_guide_multicopter_basic.md)中描述的方法调校机架，但需要使用[高级多旋翼PID调校指南（高级/详细）](../config_mc/pid_tuning_guide_multicopter.md#thrust-curve)来了解如何调整推力曲线。  

### 空中模式

在验证机体在低油门和高油门下均能良好飞行后，可以通过[MC_AIRMODE](../advanced_config/parameter_reference.md#MC_AIRMODE)参数启用[空中模式](../config_mc/pid_tuning_guide_multicopter.md#airmode)。  
此功能确保机体在低油门时仍可控并跟踪速率。