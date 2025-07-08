# 多旋翼滤波器调参与控制延迟

滤波器可以在控制延迟（影响飞行性能）和噪声过滤（影响飞行性能及电机健康）之间进行权衡。

本主题概述了控制延迟和PX4滤波器调参。

::: info
在滤波器调参前应先进行[基础多旋翼PID调参](../config_mc/pid_tuning_guide_multicopter_basic.md)。
机体需要处于欠调状态（**P**和**D**增益设置过低），以确保控制器不会产生可能被误判为噪声的振荡（默认增益可能已足够）。
:::

## 控制延迟

_control latency_（控制延迟）是指从机体物理扰动到电机对变化做出反应之间的延迟时间。

:::tip
降低延迟可以提高速率**P**增益，从而改善飞行性能。
即使延迟相差1毫秒也会产生显著影响。
:::

以下因素会影响控制延迟：

- 软式机身或软式减震装置会增加延迟（它们起到滤波器的作用）。
- 软件和传感器芯片中的低通滤波器通过增加延迟来换取更优的噪声过滤效果。
- PX4软件内部机制：传感器信号需要由驱动程序读取，再通过控制器传递到输出驱动。
- 最大陀螺仪发布频率（通过参数[IMU_GYRO_RATEMAX](../advanced_config/parameter_reference.md#IMU_GYRO_RATEMAX)配置）。
  更高的频率会降低延迟，但会增加计算负载/可能影响其他进程的运行。
  4 kHz或更高的频率仅推荐用于配备STM32H7处理器或更新型号的控制器（2 kHz是性能较弱处理器的极限值）。
- IO芯片（MAIN引脚）相比AUX引脚会增加约5.4 ms的延迟（此规则不适用于_Pixracer_或_Omnibus F4_，但适用于Pixhawk）。
  为避免IO延迟，应将电机连接到AUX引脚。
- PWM输出信号：优先启用[Dshot](../peripherals/dshot.md)以降低延迟（或在不支持DShot时使用One-Shot）。
  协议选择是在[执行器配置](../config/actuators.md)过程中为输出组设置的。

下文我们将分析低通滤波器的影响。

## 滤波器

PX4 中控制器的滤波处理流程如下所述。

::: info
采样和滤波始终以传感器原始采样率进行（通常为8kHz，具体取决于IMU）。
:::

### 陀螺仪传感器的芯片数字滤波器

在可禁用的芯片上均被禁用（若不可禁用，则截止频率设置为芯片最高值）。

### 陷波滤波器

对于存在显著低频噪声峰值的情况（例如由于桨叶通过频率的谐波导致），使用陷波滤波器可以在信号进入低通滤波器前进行清理（这些谐波对电机的负面影响与其他噪声源类似）。

如果不使用陷波滤波器，则需要将低通滤波器的截止频率设置得更低（增加相位滞后），以避免将噪声传递给电机。

#### 静态陷波滤波器

用于陀螺仪传感器数据的1-2个静态陷波滤波器，可过滤窄带噪声，例如机体的弯曲模态。

静态陷波滤波器可通过以下参数配置：

- 第一个陷波滤波器：[IMU_GYRO_NF0_BW](../advanced_config/parameter_reference.md#IMU_GYRO_NF0_BW) 和 [IMU_GYRO_NF0_FRQ](../advanced_config/parameter_reference.md#IMU_GYRO_NF0_FRQ)。
- 第二个陷波滤波器：[IMU_GYRO_NF1_BW](../advanced_config/parameter_reference.md#IMU_GYRO_NF1_BW) 和 [IMU_GYRO_NF1_FRQ](../advanced_config/parameter_reference.md#IMU_GYRO_NF1_FRQ)。

::: info
仅提供两个陷波滤波器。
具有超过两个频率噪声峰值的机体通常使用陷波滤波器清理前两个峰值，其余峰值通过低通滤波器处理。
:::

#### 动态陷波滤波器

动态陷波滤波器使用电调RPM反馈和/或机载FFT分析。
电调RPM反馈用于跟踪桨叶通过频率及其谐波，而FFT分析可用于跟踪其他振动源的频率（如燃油发动机）。

RPM反馈需要电调支持RPM反馈功能，例如连接遥测的[DShot](../peripherals/esc_motors.md#dshot)，双向DShot设置（[开发中](https://github.com/PX4/PX4-Autopilot/pull/23863)）或[UAVCAN/DroneCAN电调](../dronecan/escs.md)。
启用前需确保电调RPM值正确，可能需要调整[电机极对数](../advanced_config/parameter_reference.md#MOT_POLE_COUNT)。

需设置以下参数以启用和配置动态陷波滤波器：

| 参数                                                                                                     | 描述                                                              |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| <a href="IMU_GYRO_DNF_EN"></a>[IMU_GYRO_DNF_EN](../advanced_config/parameter_reference.md#IMU_GYRO_DNF_EN)    | 启用IMU陀螺仪动态陷波滤波。`0`: 电调RPM，`1`: 机载FFT。             |
| <a href="IMU_GYRO_FFT_EN"></a>[IMU_GYRO_FFT_EN](../advanced_config/parameter_reference.md#IMU_GYRO_FFT_EN)    | 启用机载FFT（若`IMU_GYRO_DNF_EN`设为`1`则必需）。                    |
| <a href="IMU_GYRO_DNF_MIN"></a>[IMU_GYRO_DNF_MIN](../advanced_config/parameter_reference.md#IMU_GYRO_DNF_MIN) | 动态陷波滤波器最低频率（Hz）。                                     |
| <a href="IMU_GYRO_DNF_BW"></a>[IMU_GYRO_DNF_BW](../advanced_config/parameter_reference.md#IMU_GYRO_DNF_BW)    | 每个陷波滤波器的带宽（Hz）。                                       |
| <a href="IMU_GYRO_DNF_HMC"></a>[IMU_GYRO_DNF_HMC](../advanced_config/parameter_reference.md#IMU_GYRO_NF0_BW)  | 需过滤的谐波数量。                                                 |

### 低通滤波器

通过[IMU_GYRO_CUTOFF](../advanced_config/parameter_reference.md#IMU_GYRO_CUTOFF)参数可配置陀螺仪数据的低通滤波器。

为减少控制延迟，需提高低通滤波器的截止频率。
`IMU_GYRO_CUTOFF`参数增加对延迟的影响如下表所示：

| 截止频率 (Hz) | 延迟近似值 (ms) |
| -------------- | ---------------- |
| 30             | 8                |
| 60             | 3.8              |
| 120            | 1.9              |

但这是个权衡，因为提高`IMU_GYRO_CUTOFF`也会增加传递给电机的信号噪声。
电机噪声会导致以下后果：

- 电机和电调过热，可能损坏。
- 飞行时间减少，因为电机持续改变转速。
- 可见的随机小抖动。

### D项的低通滤波器

D项最容易受噪声影响，而轻微延迟增加对性能影响不大。
因此D项具有单独可配置的低通滤波器：[IMU_DGYRO_CUTOFF](../advanced_config/parameter_reference.md#IMU_DGYRO_CUTOFF)。

### 电机输出的斜率率滤波器

电机输出的可选斜率率滤波器。
此速率可在配置执行器时作为[多旋翼几何](../config/actuators.md#motor-geometry-multicopter)的一部分进行配置（这会修改每个电机`n`的[CA_Rn_SLEW](../advanced_config/parameter_reference.md#CA_R0_SLEW)参数）。

## 滤波器调参

::: info
最佳滤波器设置取决于机体。
默认值设置较为保守，以便在低质量配置中也能正常工作。
:::

首先确保已激活高频率记录配置文件（[SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE) 参数）。
[飞行分析](../getting_started/flight_reporting.md) 将显示滚转、俯仰和偏航控制的FFT图。

:::warning

- 不要通过滤波器调参来解决高振动问题！
  应优先修复机体硬件配置。
- 确认PID增益（特别是D项）未设置过高，这可能导致振动。

:::

滤波器调参最好通过飞行日志分析完成。
可以连续进行多次飞行并调整不同参数，然后检查所有日志，但请确保在飞行间断开武装以生成独立日志文件。

执行的飞行机动可以是简单的[稳定模式](../flight_modes_mc/manual_stabilized.md)悬停，配合向各方向的滚转/俯仰以及部分油门增加阶段。
总时长无需超过30秒。
为便于比较，所有测试中的机动应保持相似。

首先通过逐步增加10Hz的方式来调整陀螺仪滤波器 [IMU_GYRO_CUTOFF](../advanced_config/parameter_reference.md#IMU_GYRO_CUTOFF)（同时使用较低的D项滤波器值 [IMU_DGYRO_CUTOFF](../advanced_config/parameter_reference.md#IMU_DGYRO_CUTOFF) = 30）。
将日志上传至 [Flight Review](https://logs.px4.io) 并比较 _Actuator Controls FFT_ 图。
将截止频率设置为噪声开始显著增加前的值（频率在60Hz及以上时）。

然后以相同方式调整D项滤波器 (`IMU_DGYRO_CUTOFF`)。
注意，如果 `IMU_GYRO_CUTOFF` 和 `IMU_DGYRO_CUTOFF` 设置差异过大（尽管差异需要显著，例如 D=15, gyro=80）可能会对性能产生负面影响。

以下是三个不同 `IMU_DGYRO_CUTOFF` 滤波器值（40Hz, 70Hz, 90Hz）的示例。
在90Hz时噪声水平开始增加（特别是滚转方向），因此70Hz是一个安全设置。
![IMU_DGYRO_CUTOFF=40](../../assets/config/mc/filter_tuning/actuator_controls_fft_dgyrocutoff_40.png)
![IMU_DGYRO_CUTOFF=70](../../assets/config/mc/filter_tuning/actuator_controls_fft_dgyrocutoff_70.png)
![IMU_DGYRO_CUTOFF=90](../../assets/config/mc/filter_tuning/actuator_controls_fft_dgyrocutoff_90.png)

::: info
该图表不能在不同机体间比较，因为Y轴量程可能不同。
在相同机体上该图表具有一致性且与飞行时长无关。
:::

如果飞行图显示明显的低频尖峰（如下方图表所示），可通过陷波滤波器消除。
此时可以使用设置：[IMU_GYRO_NF0_FRQ=32](../advanced_config/parameter_reference.md#IMU_GYRO_NF0_FRQ) 和 [IMU_GYRO_NF0_BW=5](../advanced_config/parameter_reference.md#IMU_GYRO_NF0_BW)（注意，此尖峰比通常更窄）。
低通滤波器和陷波滤波器可以独立调整（即在收集低通滤波器调参数据时无需先设置陷波滤波器）。

![IMU_GYRO_NF0_FRQ=32 IMU_GYRO_NF0_BW=5](../../assets/config/mc/filter_tuning/actuator_controls_fft_gyro_notch_32.png)

## 其他提示

1. 可接受的延迟取决于机体大小和预期。  
   FPV竞速者通常调整到绝对最小的延迟（大概`IMU_GYRO_CUTOFF`约为120，`IMU_DGYRO_CUTOFF`为50到80）。  
   对于更大的机体，延迟的重要性较低，`IMU_GYRO_CUTOFF`约为80可能可以接受。

1. 你可以从较高的`IMU_GYRO_CUTOFF`值（例如100Hz）开始调整，这可能更理想，因为`IMU_GYRO_CUTOFF`的默认设置非常低（30Hz）。  
   唯一需要注意的是风险：  
   - 飞行时间不要超过20-30秒  
   - 检查电机是否过热  
   - 注意异常噪音和过多的噪声症状，如上所述。