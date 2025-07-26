

# 使用飞行评审进行日志分析

[Flight Review](http://logs.px4.io) 提供的飞行图表可用于分析机体的整体状态。

这些图表旨在直观易懂，但需要一定的经验才能了解可接受的范围以及图表应呈现的形态。  
本页面解释如何解读这些图表并识别常见问题。

## 通用使用说明

多张图表中通用的功能：

- 图表背景颜色用于指示记录期间的飞行模式（图表内容可能因模式不同而变化）：  
  ![飞行模式](../../assets/flight_log_analysis/flight_review/flight_modes.png)  
  - **飞行模式**：图表主体的背景颜色表示当前飞行模式。  
    将鼠标悬停在图表上可显示飞行模式标签。  
  - **VTOL飞行模式**：VTOL机体在图表底部部分通过背景颜色额外显示VTOL模式（多旋翼为蓝色，固定翼为黄色，转换阶段为红色）。  
- 在特定图表轴上滚动鼠标可缩放该轴（水平或垂直方向）。  
- 在图表区域内滚动鼠标可同时缩放两个轴。

<a id="tracking"></a>

## PID跟踪性能

根据飞行模式的不同，机体控制器可能尝试跟踪位置、速度、高度或速率设定值（跟踪的设定值取决于模式，例如：在稳定模式下没有速度设定值）。

**Estimated**（红色）线应与**Setpoint**（绿色）线紧密匹配。  
若未匹配，在大多数情况下需要调整该控制器的PID增益。

[多旋翼PID调参指南](../config_mc/pid_tuning_guide_multicopter.md)包含示例图表和关于分析跟踪性能的信息。

:::tip
对于速率控制器而言，启用高频率日志记录配置文件（[SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE)）在放大查看时有助于获取更多细节信息。
:::

## 振动

振动是多旋翼机体最常见的问题之一。  
过高的振动水平可能导致：  
- 飞行效率降低，飞行时间缩短  
- 电机过热  
- 材料磨损加剧  
- 无法精确调校机体，导致飞行性能下降  
- 传感器数据截断  
- 位置估算失败，可能导致失控飞离  

因此需要持续关注振动水平，并根据需要优化设置。  

振动存在一个明显过高的临界点，通常来说振动水平越低越好。  
不过在"状态正常"和"振动过高"之间存在较大区间，这个区间取决于多个因素，包括机体尺寸——较大的机体具有更高惯性，允许使用更多软件滤波（同时大尺寸机体的振动频率更低）。  

以下段落和章节将介绍如何查看振动水平的图表以及分析方法。  

:::tip  
分析振动时查看多个图表是有价值的（不同图表可能更明显地突出某些问题）。  
:::

### 执行器控制 FFT

::: info
你需要启用高频率记录配置文件 ([SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE)) 才能查看此图表。
:::

该图表展示了基于执行器控制信号（速率控制器的 PID 输出）的横滚轴、俯仰轴和偏航轴的频率分布图。  
它有助于识别频率峰值并配置软件滤波器。  
在最低端（约 20 Hz 以下）应该只有一个峰值，其余部分应保持低且平坦。

请注意，不同机体的 y 轴缩放比例会有所不同，但来自同一机体的记录日志之间可以直接相互比较。

#### 示例：良好振动

[QAV-R 5英寸竞速机](../frames_multicopter/qav_r_5_kiss_esc_racer.md) 机架（优秀振动表现）

![低振动 QAV-R 5 竞速机 - FFT图](../../assets/flight_log_analysis/flight_review/vibrations_good_actuator_controls_fft.png)

::: info
上述机架的振动特性非常优秀，这意味着我们可以显著提高[软件滤波器](../config_mc/filter_tuning.md)的截止频率（降低控制延迟）。
:::

DJI F450 机架（良好振动表现）。

![低振动 DJI F450 - FFT图](../../assets/flight_log_analysis/flight_review/vibrations_f450_actuator_controls_fft.png)

S500 机架：

![低振动 S500 执行器控制 - FFT图](../../assets/flight_log_analysis/flight_review/vibrations_s500_actuator_controls_fft.png)

::: info
虽然上述图表看起来良好，但[同一飞行的原始加速度图](#raw_acc_s500)显示x和y方向的振动水平略高。
这是为什么要检查多个图表非常重要的一个典型案例！
:::

#### 示例：不良振动

此示例显示接近50 Hz的频率峰值（在此情况下由"松动"的起落架引起）。

![起落架振动 - FFT 图谱](../../assets/flight_log_analysis/flight_review/vibrations_landing_gear_actuator_controls_fft.png)

### 加速度功率谱密度

这是一个二维频率图，显示原始加速度计数据随时间变化的频率响应（显示x、y和z轴的总和）。区域越偏黄色，表示在该时刻和频率下的响应越高。

理想情况下，只有最低部分（至几个Hz）呈现黄色，其余区域应主要为绿色或蓝色。

#### 示例：良好振动

[QAV-R 5英寸竞速机](../frames_multicopter/qav_r_5_kiss_esc_racer.md) 机架（优秀振动）。

![低振动 QAV-R 5竞速机 - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_good_spectral.png)
<!-- https://logs.px4.io/plot_app?log=cd88b091-ec89-457c-85f6-e63e4fa0f51d -->

DJI F450 机架（良好振动）。
![低振动 DJI F450 - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_f450_spectral.png)

::: info
上方图表中可以看到螺旋桨的通过频率约为100 Hz。
:::

S500 机架：
![振动 S500 - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_s500_spectral.png)

#### 示例：不良振动

约100Hz处的强黄色线条表明存在需要进一步调查的潜在问题（首先从审查其他图表开始）。

![频谱密度图中高振动](../../assets/flight_log_analysis/flight_review/vibrations_too_high_spectral.png)

下图显示频率接近50Hz的峰值（在此情况下是由于"松动"的起落架）。

:::tip
这表明可能存在问题，因为它是一个强且单一的低频振动，接近机体动力学特性。
在默认滤波器设置为80Hz时，50Hz的振动不会被过滤。
:::

![起落架振动 - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_landing_gear_spectral.png)


极其高（不安全）的振动！请注意图表几乎完全呈现黄色。

:::warning
不应在如此高的振动水平下飞行。
:::

![频谱密度图中极高振动](../../assets/flight_log_analysis/flight_review/vibrations_exceedingly_high_spectral.png)

### 原始加速度

此图表显示了x、y和z轴的原始加速度计测量值。  
理想情况下每条线都应清晰且能明确显示机体的加速度。

经验法则是，如果在悬停或慢速飞行期间z轴图表接触到x/y轴图表，则振动水平过高。

:::tip
使用此图表的最佳方法是稍微放大到机体悬停的部分。
:::

#### 示例：良好振动

[QAV-R 5" Racer](../frames_multicopter/qav_r_5_kiss_esc_racer.md) 机架（振动极佳）。

![低振动 QAV-R 5 机架 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_good_accel.png)

DJI F450 机架（良好振动）。
![低振动 DJI F450 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_f450_accel.png)

<!-- https://logs.px4.io/plot_app?log=cd88b091-ec89-457c-85f6-e63e4fa0f51d -->

#### 示例：振动问题

<a id="raw_acc_s500"></a>
S500机身。振动水平接近临界值，x轴和y轴的振动稍高（这在S500机身中较为常见）。  
这已接近开始对飞行性能产生负面影响的临界点。

![临界振动水平 S500 x/y轴 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_s500_accel.png)

振动水平过高。注意z轴的图像如何与x/y轴图像重叠：

![起落架振动 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_landing_gear_accel.png)

振动水平过高。注意z轴的图像如何与x/y轴图像重叠：

![高振动 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_too_high_accel.png)

振动水平极高（存在安全隐患）。

:::warning
您不应以如此高的振动水平飞行。
:::

![极端高振动 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_exceedingly_high_accel.png)


<a id="fifo_logging"></a>

### 原始高采样率IMU数据图表

为了深入分析，可以选择以全速率（取决于IMU，通常为数千赫兹）记录原始IMU数据。这可以检测比普通日志更高频率的信号，有助于选择合适的减震装置或正确配置低通和陷波滤波器。

要使用此功能，需要修改以下参数：
- 将[IMU_GYRO_RATEMAX](../advanced_config/parameter_reference.md#IMU_GYRO_RATEMAX) 设置为400。
  这确保传感器数据在从传感器传输到系统其他部分时更高效地打包，并减小日志文件体积（不会减少有用数据）。
  <!-- Explanation in https://github.com/PX4/PX4-user_guide/pull/751/files#r440509688
  Data is sent in a fixed size array that will largely empty if sent at higher rate. The "empty data" is also logged.-->
- 使用优质SD卡，因为IMU数据需要高日志带宽（当记录速率过高时，Flight Review将显示数据丢失）。

  :::tip
  查看[Logging > SD Cards](../dev_log/logging.md#sd-cards)了解主流SD卡的对比。
  :::

- 在[SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE)中启用陀螺仪或加速度计的高采样率FIFO配置文件，并禁用其他条目。
  如果使用优质SD卡（几乎看不到数据丢失），可以：
  - 同时启用加速度计和陀螺仪的配置文件
  - 或启用加速度计/陀螺仪配置文件和默认日志配置文件

示例图表：

![高采样率加速度计功率谱密度](../../assets/flight_log_analysis/flight_review/accel_spectral_density_fifo.png)

::: info
记录的是第一个IMU的数据，这与飞行使用的IMU可能不同。
这在IMU安装方式不同的情况下（例如硬安装与软安装）尤为重要。
:::

::: info
测试完成后不要忘记恢复参数设置。
:::

<a id="solutions"></a>

### 解决振动问题

通常仅凭日志无法识别振动源（或多种振动源的组合）。

在这种情况下，应检查机体。
[Vibration Isolation](../assembly/vibration_isolation.md) 解释了一些可以检查（并执行）以降低振动水平的基本事项。

## 执行器输出

*执行器输出* 图表显示发送到各个执行器（电机/舵机）的信号。通常其范围介于配置的最小和最大PWM值之间（例如从1000到2000）。

这是一个四旋翼的示例，所有信号都在范围内，大致重叠且噪声不高：  
![Good actuator outputs](../../assets/flight_log_analysis/flight_review/actuator_outputs_good.png)

该图表可帮助识别以下问题：  
- 如果一个或多个信号长时间处于最大值，则表示控制器进入了**饱和**。  
  这不一定是问题，例如在油门全开时这是预期现象。  
  但如果在任务期间发生这种情况，表明机体的重量超过了其可提供的推力。  
- 对于多旋翼，图表可以很好地表明机体是否**不平衡**。  
  图表显示，一个或多个相邻电机（四旋翼情况下为两个电机）需要平均运行在更高的推力上。  
  请注意，这种情况也可能发生在某些电机提供的推力不同或电调未校准的情况下。  
  不平衡的机体通常不是大问题，因为自动驾驶仪会自动进行补偿。  
  但会降低最大可实现的推力，并对某些电机施加更多压力，因此最好平衡机体。  
- 不平衡也可能来自偏航轴。  
  图表看起来与前一种情况类似，但相对的电机将分别运行在更高或更低的推力上。  
  原因可能是有一个或多个电机倾斜。  

  这是一个六旋翼的示例：电机1、3和6运行在更高的推力上：  
  ![Hexrotor imbalanced actuator outputs](../../assets/flight_log_analysis/flight_review/actuator_outputs_hex_imbalanced.png)  
  <!-- https://logs.px4.io/plot_app?log=9eca6934-b657-4976-a32f-b2e56535f05f -->  
- 如果信号看起来非常**噪声**（振幅高），可能有两个原因：传感器噪声或通过控制器的振动（这也会在其他图表中显示，见上一节）或PID增益过高。  
  这是一个极端示例：  
  ![Noisy actuator outputs - extreme case](../../assets/flight_log_analysis/flight_review/actuator_outputs_noisy.png)

## GPS不确定性

*GPS不确定性*图表显示了来自GPS设备的信息：
- 使用的卫星数量（应为12或更高）
- 水平定位精度（应低于1米）
- 垂直定位精度（应低于2米）
- GPS定位类型：3表示3D GPS定位，4表示GPS+航位推算，5表示RTK float，6表示RTK fixed

## GPS噪声与干扰

GPS噪声与干扰图可用于检查GPS信号干扰和阻塞情况。  
GPS信号非常微弱，因此很容易受到通过电缆传输或以GPS使用频率辐射的组件干扰/阻塞。

:::tip  
USB 3 [已知会](https://www.usb.org/sites/default/files/327216.pdf) 有效阻塞GPS信号。  
:::

**干扰指示器** 的数值应在40或以下。  
80或更高的数值过高，需检查设备安装情况。  
信号干扰还会表现为精度降低、可用卫星数量减少，直至完全无法获取GPS定位。

这是一个无干扰的示例：

![GPS干扰 - 良好图表](../../assets/flight_log_analysis/flight_review/gps_jamming_good.png)

## 推力和磁场

*推力和磁场* 图表显示了推力和磁传感器测量向量的模。

该模值在整个飞行过程中应保持恒定且与推力无关。
这是一个模值非常接近恒定的良好示例：
![推力和磁场接近恒定](../../assets/flight_log_analysis/flight_review/thrust_and_mag_good.png)

*如果存在相关性*，则表示电机（或其他耗电设备）的电流正在影响磁场。
这必须避免，因为它会导致偏航估计错误。
下图展示了推力与磁力计模值的强相关性：
![相关推力和磁场](../../assets/flight_log_analysis/flight_review/thrust_and_mag_correlated.png)

解决方案包括：
- 使用外部磁力计（避免使用内置磁力计）
- 如果使用外部磁力计，将其远离强电流区域（例如通过使用更长的GPS天线杆）

如果模值不相关但不恒定，很可能校准不当。
但也可能是由于外部干扰（例如飞行时靠近金属结构）。

此示例显示模值非恒定，但与推力不相关：
![不相关推力和磁场](../../assets/flight_log_analysis/flight_review/thrust_and_mag_uncorrelated_problem.png)

## 估算器看门狗

*估算器看门狗*图表显示估算器的健康状态报告。它应该始终为零。

如果没有问题，图表应显示如下：
![Estimator watchdog - good](../../assets/flight_log_analysis/flight_review/estimator_watchdog_good.png)

如果其中一个标志位不为零，表示估算器检测到需要进一步调查的问题。大部分情况是传感器相关的问题，例如磁强计干扰。通常建议查看对应传感器的图表数据。
<!-- TODO: separate page for estimator issues? -->

以下是磁强计问题的示例：
![Estimator watchdog with magnetometer problems](../../assets/flight_log_analysis/flight_review/estimator_watchdog_mag_problem.png)

## 传感器数据的采样规律性

采样规律性图表可帮助分析日志系统和调度问题。

当日志缓冲区过小、日志频率过高或使用低质量SD卡时，**日志丢包**数量会开始增加。

::: info
中等质量的SD卡偶尔出现丢包是正常现象。
:::

**delta t** 显示两次记录的IMU采样之间的时间差。
该值应接近4 ms，因为数据发布频率为250Hz。
如果出现该周期的倍数级峰值（且估计器时间滑移未增加），说明记录器跳过了部分采样。
偶尔会发生这种情况，因为记录器优先级较低。
如果出现非倍数级峰值，表示传感器驱动调度异常，需要进一步排查。

**estimator timeslip** 显示当前时间与集成传感器间隔时间的差值。
如果该值发生变化，说明估计器错过了传感器数据或驱动发布了错误的集成间隔。
该值应保持为0，但飞行中参数更改时可能略微增加，通常不构成问题。

这是一个良好示例：
![采样规律性良好](../../assets/flight_log_analysis/flight_review/sampling_regularity_good.png)

以下示例包含过多丢包，这是由于使用的SD卡质量过低导致的
(查看[此处](../dev_log/logging.md#sd-cards)获取推荐的SD卡)：

![大量丢包](../../assets/flight_log_analysis/flight_review/sampling_regularity_many_drops.png)

## 记录的消息

这是一个包含系统错误和警告消息的表格。
例如它们会在任务堆栈空间不足时显示。

消息需要单独分析，并非所有消息都表示存在问题。
例如以下显示的是杀伤开关测试：
![记录的消息](../../assets/flight_log_analysis/flight_review/logged_messages.png)

## 飞行/机体日志审查示例

在分析机体状态时，查看特定飞行的多个图表通常很有价值（不同图表可以更好地突出某些问题）。  
在审查可能的振动问题时这一点尤为重要。

以下部分将一些（之前提到的）图表按飞行/机体进行分组。

### QAV-R 5" Racer

这些图表均来自同一次[QAV-R 5" Racer](../frames_multicopter/qav_r_5_kiss_esc_racer.md)的飞行。
<!-- https://logs.px4.io/plot_app?log=cd88b091-ec89-457c-85f6-e63e4fa0f51d -->

它们展示了一架振动非常低的机体：
- 执行器控制FFT仅在最低端显示一个峰值，其余部分低且平直。
- 频谱密度大部分为绿色，低频部分仅有少量黄色。
- 原始加速度的Z轴轨迹与X/Y轴轨迹明显分离。

![低振动QAV-R 5 Racer - FFT图表](../../assets/flight_log_analysis/flight_review/vibrations_good_actuator_controls_fft.png)

![低振动QAV-R 5 Racer - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_good_spectral.png)

![低振动QAV-R 5 Racer - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_good_accel.png)

### DJI F450

这些图表均来自同一次 *DJI F450* 飞行测试。  
<!-- https://logs.px4.io/plot_app?log=cd88b091-ec89-457c-85f6-e63e4fa0f51d -->

它们展示了一个振动较低的机体（但比上面的QAV-R稍高）：
- 执行器控制FFT图显示最低端出现峰值。  
  其余大部分区域平坦，除了约100Hz处出现一个波峰（这是螺旋桨的桨叶通过频率）。
- 频谱密度主要为绿色。桨叶通过频率再次可见。
- 原始加速度的Z轴轨迹与X/Y轴轨迹明显分离。

![低振动DJI F450 - FFT图](../../assets/flight_log_analysis/flight_review/vibrations_f450_actuator_controls_fft.png)

![低振动DJI F450 - 频谱密度图](../../assets/flight_log_analysis/flight_review/vibrations_f450_spectral.png)

![低振动DJI F450 - 原始加速度图](../../assets/flight_log_analysis/flight_review/vibrations_f450_accel.png)

### S500

这些图表均来自同一架S500的飞行记录。

它们展示了一架机体振动接近可接受极限的情况：
- 执行器控制FFT图显示在最低端有一个峰值。  
  其余大部分区域较为平坦，除了约100Hz处出现一个凸起。
- 频谱密度主要为绿色，但在100Hz处比DJI F450的黄色更多。
- 原始加速度的z轴轨迹与x/y轴轨迹较为接近。  
  这是在开始对飞行性能产生负面影响的极限位置。

![Low vibration S500 actuator controls - FFT plot](../../assets/flight_log_analysis/flight_review/vibrations_s500_actuator_controls_fft.png)

![Vibration S500 - spectral density plot](../../assets/flight_log_analysis/flight_review/vibrations_s500_spectral.png)

![Borderline vibration S500 x, y - raw accel. plot](../../assets/flight_log_analysis/flight_review/vibrations_s500_accel.png)