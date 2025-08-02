

# 使用PX4的导航滤波器(EKF2)

本教程解答了关于导航中使用的扩展卡尔曼滤波（EKF）算法的常见问题。

:::tip
来自_PX4开发者峰会2019_（保罗·莱斯伯勒博士）的视频[PX4状态估计概述](https://youtu.be/HkYRJJoyBwQ)概述了估计器，并进一步描述了2018/2019年以来的主要变化，以及此后添加的重大改进和优化。
:::

## 概述

PX4 的导航滤波器采用扩展卡尔曼滤波器（Extended Kalman Filter, EKF）算法处理传感器测量数据，提供以下状态的估计：

- 定义从北-东-下局部导航框架到机体 X/Y/Z 坐标系的旋转四元数
- 惯性测量单元（IMU）处的速度 - 北/东/下（m/s）
- IMU 处的位置 - 纬度（rad）、经度（rad）、高度（m）
- IMU 陀螺仪偏差估计值 - X/Y/Z（rad/s）
- IMU 加速度计偏差估计值 - X/Y/Z（m/s²）
- 地球磁场分量 - 北/东/下（高斯）
- 机体坐标系磁场偏差 - X/Y/Z（高斯）
- 风速 - 北/东（m/s）
- 地形高度（m）

为提高稳定性，采用了"误差状态"公式表示。这在估计旋转的不确定性（SO(3)的切空间）时尤为重要。

EKF 在延迟的"融合时间窗口"上运行，允许每个传感器相对于 IMU 的不同时间延迟。每个传感器的数据通过 FIFO 缓冲存储，由 EKF 在正确时间从缓冲区读取使用。每个传感器的延迟补偿由 [EKF2_*_DELAY](../advanced_config/parameter_reference.md#ekf2) 参数控制。

一个互补滤波器用于通过缓冲的 IMU 数据将状态从"融合时间窗口"传播到当前时间。该滤波器的时间常数由 [EKF2_TAU_VEL](../advanced_config/parameter_reference.md#EKF2_TAU_VEL) 和 [EKF2_TAU_POS](../advanced_config/parameter_reference.md#EKF2_TAU_POS) 参数控制。

::: info
"融合时间窗口"的延迟和缓冲区长度由 [EKF2_DELAY_MAX](../advanced_config/parameter_reference.md#EKF2_DELAY_MAX) 确定。该值应至少等于最长延迟 `EKF2_*_DELAY`。减少"融合时间窗口"延迟会降低用于将状态传播到当前时间的互补滤波器的误差。
:::

EKF 仅使用 IMU 数据进行状态预测。IMU 数据在 EKF 推导中不作为观测使用。协方差预测和测量雅可比矩阵的代数方程使用 [SymForce](https://symforce.org/) 推导，代码可在此查看：[Symbolic Derivation](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/ekf2/EKF/python/ekf_derivation/derivation.py)。使用 [Joseph Stabilized form](https://en.wikipedia.org/wiki/Kalman_filter#Deriving_the_posteriori_estimate_covariance_matrix) 进行协方差更新，以提高数值稳定性和支持独立状态的条件更新。

### 关于位置输出的精度说明

位置通过纬度、经度和高度进行估算，惯性导航系统（INS）的积分采用WGS84椭球模式。  
然而，位置不确定性是在当前位置的局部导航坐标系中定义的（即：北东地坐标系下的误差，单位为米）。

在将位置和速度状态输出到控制回路之前，会调整IMU与机体坐标系之间的偏移量。

IMU相对于机体坐标系的位置由参数`EKF2_IMU_POS_X,Y,Z`设定。

除了以纬度/经度/高度表示的全局位置估算值外，滤波器还通过将全局位置估算值投影到任意原点为中心的[azimuthal_equidistant_projection](https://en.wikipedia.org/wiki/Azimuthal_equidistant_projection)坐标系，提供局部位置估算值（北东地坐标系下，单位为米）。  
当融合全局位置测量数据时，该原点会自动设定，但也可以手动指定。  
如果没有提供全局位置信息，仅可使用局部位置，并且惯性导航系统（INS）的积分将在球形地球模型上进行。

## 运行单个EKF实例

默认行为是运行单个EKF实例。在此情况下，传感器选择和故障切换在数据被EKF接收之前进行。这可以防止有限数量的传感器故障（例如数据丢失），但无法防止传感器提供超出EKF和控制环补偿能力的不准确数据。

运行单个EKF实例的参数设置如下：

- [EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) = 0
- [EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) = 0
- [SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE) = 1
- [SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE) = 1

## 运行多个EKF实例

根据惯性测量单元（IMU）和磁力计的数量以及飞控的CPU能力，可以运行多个EKF实例。这通过每个EKF实例使用不同传感器组合来实现，从而提供更广泛的传感器错误保护。通过比较每个EKF实例的内部一致性，EKF选择器可以确定数据一致性最佳的EKF和传感器组合。这使得能够检测并隔离如IMU偏置突变、饱和或数据卡死等故障。

EKF实例的总数是IMU数量与[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU)和磁力计数量与[EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG)的乘积，计算公式如下：

> 实例数量 = MAX([EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) , 1) x MAX([EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) , 1)

例如，具有2个IMU和2个磁力计的飞控可以通过设置EKF2_MULTI_IMU = 2和EKF2_MULTI_MAG = 2运行4个EKF实例，每个实例使用以下传感器组合：

- EKF实例1：IMU 1，磁力计1
- EKF实例2：IMU 1，磁力计2
- EKF实例3：IMU 2，磁力计1
- EKF实例4：IMU 2，磁力计2

理论上最多可处理4个IMU和4个磁力计传感器，形成4 x 4 = 16个EKF实例。实际数量受限于可用计算资源。在该功能开发过程中，基于STM32F7 CPU的硬件测试显示，4个EKF实例在可接受的处理负载和内存占用范围内运行。

:::warning
在飞行前应进行基于地面的测试，以检查CPU和内存使用情况。
:::

如果[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) >= 3，则大速率陀螺仪错误的故障切换时间将进一步缩短，因为EKF选择器可以采用中值选择策略以更快隔离故障IMU。

多个EKF实例的配置由以下参数控制：

- [SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE):
  如果运行具有IMU传感器多样性的多个EKF实例（即[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) > 1），需设置为0。

  设置为1时（单EKF操作默认值），传感器模块会为EKF选择IMU数据。这可防止传感器数据丢失，但无法防范错误传感器数据。设置为0时，传感器模块不进行选择。

- [SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE):
  如果运行具有磁力计传感器多样性的多个EKF实例（即[EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) > 1），需设置为0。

  设置为1时（单EKF操作默认值），传感器模块会为EKF选择磁力计数据。这可防止传感器数据丢失，但无法防范错误传感器数据。设置为0时，传感器模块不进行选择。

- [EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU):
  该参数指定多个EKF使用的IMU传感器数量。若`EKF2_MULTI_IMU` <= 1，则仅使用第一个IMU传感器。当[SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE) = 1时，此为传感器模块选择的传感器。若`EKF2_MULTI_IMU` >= 2，则将为指定数量的IMU传感器运行单独的EKF实例，最多不超过4个或实际存在的IMU数量中的较小值。

- [EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG):
  该参数指定多个EKF使用的磁力计传感器数量。若`EKF2_MULTI_MAG` <= 1，则仅使用第一个磁力计传感器。当[SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE) = 1时，此为传感器模块选择的传感器。若`EKF2_MULTI_MAG` >= 2，则将为指定数量的磁力计传感器运行单独的EKF实例，最多不超过4个或实际存在的磁力计数量中的较小值。

::: info
不支持记录和[EKF2回放](../debug/system_wide_replay.md#ekf2-replay)具有多个EKF实例的飞行日志。要启用EKF回放记录，必须将参数设置为启用[单个EKF实例](#运行单个EKF实例)。
:::

## 它使用哪些传感器测量数据？

EKF 具有多种运行模式，允许使用不同的传感器测量组合。
启动时，滤波器会检查传感器的最小可行组合，初始的翻滚/偏航/高度对齐完成后，进入提供旋转、垂直速度、垂直位置、IMU 角度偏差和 IMU 速度偏差估计的模式。

此模式需要 IMU 数据、偏航来源（磁力计或外部视觉）和高度数据来源。
所有 EKF 运行模式都需要这个最小数据集。
其他传感器数据可用于估计额外状态。

### IMU

- 三轴机体固定的惯性测量单元角增量和速度增量数据，最低采样频率为100Hz。
  注意：在将IMU角增量数据用于EKF之前，应应用圆锥校正。

### 磁强计

估计器至少需要以5Hz的频率获取三轴机体固定磁强计数据。

::: info

- 磁强计的**偏移**仅在无人机旋转时可被观测  
- 当机体加速（线性加速度）且绝对位置或速度测量值被融合（如GPS）时，真实航向可被观测  
  这意味着如果满足上述条件足够频繁以约束航向漂移（由陀螺仪偏移引起），初始化后磁强计航向测量可选  

::: 

可通过 [EKF2_MAG_TYPE](../advanced_config/parameter_reference.md#EKF2_MAG_TYPE) 配置磁强计数据融合：

0. 自动：  
   - 磁强计读数仅在解锁前影响航向估计，解锁后影响完整姿态  
   - 使用此方法时航向和倾斜误差会得到补偿  
   - 错误的磁场测量会降低倾斜估计精度  
   - 磁强计偏移在可被观测时进行估计  

1. 磁航向：  
   - 仅修正航向  
     倾斜估计永远不受错误磁场测量影响  
   - 当飞行时无速度/位置辅助的情况下，倾斜误差不会被修正  
   - 磁强计偏移在可被观测时进行估计  

2. 已弃用  
3. 已弃用  
4. 已弃用  
5. 无：  
   - 从不使用磁强计数据  
     当数据永远不可信时（如传感器附近存在高电流、外部异常）此选项有用  
   - 估计器将使用其他航向源：[GPS航向](#偏航测量) 或外部视觉  
   - 当仅使用GPS测量且无其他航向源时，航向需在足够水平加速度后才能初始化  
     参见下文 [根据机体运动估计偏航角](#从GPS速度获取偏航角)  

6. 仅初始化：  
   - 仅在初始化阶段使用磁强计数据  
     当数据仅在解锁前可用时（如解锁后存在高电流）此选项有用  
   - 初始化后，航向通过其他观测值约束  
   - 与mag type `None`不同，当结合GPS测量时，此方法允许在起飞阶段直接运行位置控制模式  

可使用以下选择树选择合适选项：

![EKF磁强计类型选择树](../../assets/config/ekf/ekf_mag_type_selection_tree.png)

### 高度

需要至少5Hz频率的高数据源 - GPS、气压计、测距仪、外部视觉系统或这些传感器的组合。

如果未检测到所选测量值，EKF将不会启动。
当检测到这些测量值后，EKF将初始化状态并完成倾斜和偏航对齐。
当倾斜和偏航对齐完成后，EKF可以切换到其他工作模式以使用额外的传感器数据：

每个高度数据来源都可以通过其专用控制参数启用/禁用：

- [GNSS/GPS](#gnss-gps): [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)
- [气压计](#气压计): [EKF2_BARO_CTRL](../advanced_config/parameter_reference.md#EKF2_BARO_CTRL)
- [测距仪](#测距仪): [EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL)
- [外部视觉系统](#外部视觉系统): 当 [EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF) 设置为"Vision"时启用

长期来看，高度估计值会跟随"参考高度源"的数据。
此参考源由 [EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF) 参数定义。

#### 典型配置

|                           | [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) | [EKF2_BARO_CTRL](../advanced_config/parameter_reference.md#EKF2_BARO_CTRL) | [EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL) | [EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF) |
| ------------------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| 室外(默认)                | 7 (经度/纬度/高度/速度)                                                  | 1 (启用)                                                                   | 1 ([条件性](#条件范围辅助))                                | 1 (GNSS)                                                             |
| 室内(非平坦地形)          | 0 (禁用)                                                                 | 1 (启用)                                                                   | 1 ([条件性](#条件范围辅助))                                | 2 (距离)                                                             |
| 室内(平坦地形)            | 0 (禁用)                                                                 | 1 (启用)                                                                   | 2 ([始终启用](#距离高度融合))                                   | 2 (距离)                                                             |
| 外部视觉                  | 按需                                                                     | 按需                                                                       | 按需                                                                   | 3 (视觉)                                                             |

### 气压计

通过 [EKF2_BARO_CTRL](../advanced_config/parameter_reference.md#EKF2_BARO_CTRL) 启用/禁用 [高度](#高度) 数据的气压计来源。

请注意，即使存在多个气压计，也仅融合一个气压计的数据。系统优先选择 [CAL_BAROx_PRIO](../advanced_config/parameter_reference.md#CAL_BARO0_PRIO) 优先级值最高的气压计，当检测到传感器故障时会回退到优先级次高的气压计。若多个气压计具有相同最高优先级，则使用最先检测到的气压计。可通过将某气压计的 `CAL_BAROx_PRIO` 值设置为 `0`（禁用）来完全禁用其作为数据源。

参见 [高度](#高度) 获取高度来源配置的更多细节。

#### 静态压力位置误差校正

机体气压高度会受到气动干扰的影响，这些干扰由机体相对风速和姿态引起，这在航空领域被称为_static pressure position error_（静态压力位置误差）。使用ECL/EKF2估计器库的EKF2模块提供了一种补偿这些误差的方法，前提是风速状态估计功能处于激活状态。

对于在固定翼模式下运行的机体，风速状态估计需要启用[Airspeed](#空速)（空速）和/或[Synthetic Sideslip](#合成侧滑)（合成侧滑）融合功能。

对于多旋翼，可以启用并调整[Drag Specific Forces](#mc_wind_estimation_using_drag)（阻力特定力）融合以提供所需的风速状态估计。

EKF2模块将误差建模为一个机体固定椭球体，该椭球体指定了动压的百分比在转换为气压高度估计值前被加到/减去气压高度的部分。

良好的调参步骤如下：

1. 在[Position mode](../flight_modes_mc/position.md)（位置模式）下反复飞行，机体在静止与最大速度之间前后/左右/上下移动（最佳效果需在无风条件下测试）。
2. 使用[QGroundControl: Analyze > Log Download](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/log_download.html)提取`.ulg`日志文件

   ::: info
   同一份日志文件可用于调整[多旋翼风估计器](#mc_wind_estimation_using_drag)。
   :::

3. 使用日志文件配合[baro_static_pressure_compensation_tuning.py](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/ekf2/EKF/python/tuning_tools/baro_static_pressure_compensation) Python脚本获取最佳参数集。

调参参数：

- [EKF2_PCOEF_XP](../advanced_config/parameter_reference.md#EKF2_PCOEF_XP)
- [EKF2_PCOEF_XN](../advanced_config/parameter_reference.md#EKF2_PCOEF_XN)
- [EKF2_PCOEF_YP](../advanced_config/parameter_reference.md#EKF2_PCOEF_YP)
- [EKF2_PCOEF_YN](../advanced_config/parameter_reference.md#EKF2_PCOEF_YN)
- [EKF2_PCOEF_Z](../advanced_config/parameter_reference.md#EKF2_PCOEF_Z)

#### 气压计偏差补偿

在恒定高度下，气压计的测量值可能会因环境气压环境的变化或传感器温度的变化而产生漂移。  
为补偿这一测量误差，EKF2 使用 GNSS 高度（如果可用）作为"无漂移"参考来估计该偏差。  
无需调校。

### GNSS/GPS

#### 位置和速度测量

如果满足以下条件，将使用GPS测量进行位置和速度估算：

- 通过设置[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)参数启用GPS使用。
- GPS质量检查已通过。
  这些检查由[EKF2_GPS_CHECK](../advanced_config/parameter_reference.md#EKF2_GPS_CHECK)和`EKF2_REQ_*`参数控制。

关于高度源配置的更多细节，[点击此处](#高度)。

#### 偏航测量

某些GPS接收器（如[Trimble MB-Two RTK GPS接收器](https://www.trimble.com/Precision-GNSS/MB-Two-Board.aspx)）可提供航向测量数据，以替代磁力计数据的使用。  
在存在较大磁异常的环境，或地球磁场倾角较高的纬度地区，这种替代方式具有显著优势。  
通过将[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)参数的第3位设置为1（即加上8），可以启用GPS偏航测量功能。

#### 从GPS速度获取偏航角

EKF内部运行一个额外的多假设滤波器，使用多个包含3个状态的扩展卡尔曼滤波器（EKF），其状态包括北东方向速度和偏航角。这些单独的偏航角估计值通过高斯求和滤波器（GSF）进行融合。每个3状态EKF使用IMU和GPS水平速度数据（可选空速数据），无需依赖任何先验的偏航角信息或磁强计测量数据。这为来自主滤波器的偏航角提供了备用方案，并在起飞后导航丢失表明磁强计的偏航估计失效时，用于重置主24状态EKF的偏航角。这将导致地面控制站（GCS）显示`Emergency yaw reset - magnetometer use stopped`信息提示。

当启用ekf2回放日志记录时，该估算器的数据会被记录，并可通过`yaw_estimator_status`消息查看。来自各个3状态EKF偏航估算器的单独偏航估计值位于`yaw`字段中。GSF融合后的偏航估计值在`yaw_composite`字段中。GSF偏航估计的方差在`yaw_variance`字段中。所有角度均以弧度为单位。GSF应用于各个3状态EKF输出的权重值在`weight`字段中。

这也使得在没有磁强计数据或双天线GPS接收器的情况下操作成为可能，前提是起飞后能执行足够的水平移动以使偏航角可观测。要使用此功能，将[EKF2_MAG_TYPE](../advanced_config/parameter_reference.md#EKF2_MAG_TYPE)设置为`none` (5)以禁用磁强计使用。一旦机体完成足够水平移动使偏航角可观测，主24状态EKF将将其偏航角对齐到GSF估计值，并开始使用GPS。

#### 双接收机

通过算法可根据报告的准确性混合GPS接收机数据（最佳效果需要两个接收机以相同速率输出数据且使用相同的精度指标）。该机制在接收机数据丢失时提供自动故障转移功能（例如允许标准GPS作为RTK接收机的备用）。
通过参数[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)控制此功能。

[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)参数默认设置为禁用混合功能并始终使用第一个接收机，因此需要设置该参数来选择使用哪个接收机的精度指标决定混合比例。当使用不同型号接收机时，需确保[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)参数值使用两个接收机都支持的精度指标。例如除非两个接收机驱动都发布可比的`s_variance_m_s`字段值，否则不要将位0设置为`true`（该字段位于`vehicle_gps_position`消息中）。由于不同厂商对接收精度的定义方式不同（如CEP与1-sigma等），这在跨厂商设备间可能较难实现。

设置时需检查以下内容：

- 验证第二个接收机数据是否存在
  该数据会被记录为`vehicle_gps_position_1`，也可通过_nsh控制台_使用命令`listener vehicle_gps_position -i 1`进行检查。需要正确设置参数[GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)。
- 检查每个接收机的`s_variance_m_s`、`eph`和`epv`数据，决定可用的精度指标
  如果两个接收机都输出合理的`s_variance_m_s`和`eph`数据，且导航未直接使用GPS垂直位置，则建议将[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)设置为3。若仅存在`eph`数据且两个接收机均不输出`s_variance_m_s`数据，则将[SENS_GPS_MASK](../advanced_config/parameter_reference.md#SENS_GPS_MASK)设为2。只有当GPS被选为高度参考源（通过参数[EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF)设置）且两个接收机均输出合理的`epv`数据时才设置位2。
- 混合接收机数据输出将记录为`ekf_gps_position`，可通过nsh终端使用命令`listener ekf_gps_position`进行检查。
- 若接收机输出速率不同，混合输出速率将与较慢接收机一致。建议尽可能将接收机配置为相同输出速率。

#### GNSS性能要求

为了使ECL接受GNSS数据用于导航，需要在一段时间内满足最低要求，该时段由[EKF2_REQ_GPS_H](../advanced_config/parameter_reference.md#EKF2_REQ_GPS_H)定义（默认10秒）。

最低要求在[EKF2*REQ*\*](../advanced_config/parameter_reference.md#EKF2_REQ_EPH)参数中定义，每个检查可通过[EKF2_GPS_CHECK](../advanced_config/parameter_reference.md#EKF2_GPS_CHECK)参数启用/禁用。

下表展示了直接报告或从GNSS数据中计算的不同指标，以及ECL使用数据所需的最低要求。
此外，_平均值_列显示了标准GNSS模块（例如u-blox M8系列）可能合理获得的典型值，即被认为是良好/可接受的值。

| 指标               | 最低要求                                                                          | 平均值 | 单位 | 注释                                                                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------- | ------------- | ----- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| eph                  | <&nbsp;3 ([EKF2_REQ_EPH](../advanced_config/parameter_reference.md#EKF2_REQ_EPH))         | 0.8           | m     | 水平位置误差的标准差                                                                                             |
| epv                  | <&nbsp;5 ([EKF2_REQ_EPV](../advanced_config/parameter_reference.md#EKF2_REQ_EPV))         | 1.5           | m     | 垂直位置误差的标准差                                                                                               |
| 卫星数量           | ≥6&nbsp;([EKF2_REQ_NSATS](../advanced_config/parameter_reference.md#EKF2_REQ_NSATS))      | 14            | -     |
| sacc                 | <&nbsp;0.5 ([EKF2_REQ_SACC](../advanced_config/parameter_reference.md#EKF2_REQ_SACC))     | 0.2           | m/s   | 水平速度误差的标准差                                                                                                |
| fix type             | ≥&nbsp;3                                                                                  | 4             | -     | 0-1: 无定位，2: 2D定位，3: 3D定位，4: RTCM码差分，5: 实时动态定位（浮动解），6: 实时动态定位（固定解），8: 外推 |
| PDOP                 | <&nbsp;2.5 ([EKF2_REQ_PDOP](../advanced_config/parameter_reference.md#EKF2_REQ_PDOP))     | 1.0           | -     | 位置精度衰减因子                                                                                                              |
| 水平位置漂移率      | <&nbsp;0.1 ([EKF2_REQ_HDRIFT](../advanced_config/parameter_reference.md#EKF2_REQ_HDRIFT)) | 0.01          | m/s   | 从报告的GNSS位置计算的漂移率（静态时）。                                                                        |
| 垂直位置漂移率      | <&nbsp;0.2 ([EKF2_REQ_VDRIFT](../advanced_config/parameter_reference.md#EKF2_REQ_VDRIFT)) | 0.02          | m/s   | 从报告的GNSS高度计算的漂移率（静态时）。                                                                        |
| 水平速度             | <&nbsp;0.1 ([EKF2_REQ_HDRIFT](../advanced_config/parameter_reference.md#EKF2_REQ_HDRIFT)) | 0.01          | m/s   | 报告的GNSS水平速度的滤波幅值。                                                                                    |
| 垂直速度             | <&nbsp;0.2 ([EKF2_REQ_VDRIFT](../advanced_config/parameter_reference.md#EKF2_REQ_VDRIFT)) | 0.02          | m/s   | 报告的GNSS垂直速度的滤波幅值。                                                                                      |

::: info
`hpos_drift_rate`、`vpos_drift_rate`和`hspd`在10秒期间内计算并发布在`ekf2_gps_drift`主题中。
注意`ekf2_gps_drift`不会被记录！
:::

### 测距仪

[测距仪](../sensor/rangefinders.md)的地面距离由单个状态滤波器用于估算相对于基准高度的地形垂直位置。

融合模式的操作由 [EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL) 控制：

1. [条件测距辅助](#条件范围辅助)  
1. [测距高度融合](#距离高度融合)  

关于高度源配置的更多细节，请[点击此处](#高度)。

#### 条件范围辅助

条件测距仪融合（又名_条件范围辅助_）会在低速/低空操作时激活测距仪融合用于高度估计（同时其他活动的高度源也会被使用）。
如果测距仪被设置为参考高度源（通过[EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF)），其他活动的高度源如气压计和GNSS高度计会随时间调整其测量值以匹配测距仪的读数。
当条件不满足启动范围辅助时，会自动选择次级参考源。

::: info
切换高度参考源会导致绝对高度估计值随时间漂移。
这在位置模式飞行时不是问题，但如果无人机需要按照特定GNSS高度执行任务时可能会有问题。
如果不需要绝对高度漂移，建议将GNSS高度设置为参考源，即使使用条件范围辅助时也应如此。
:::

该功能主要针对_起飞和着陆_场景，当气压计设置导致旋翼气流干扰过大而可能破坏EKF状态估计时使用。

范围辅助也可用于改善机体静止时的高度保持性能。

:::tip
推荐使用[地形保持](../flying/terrain_following_holding.md#terrain_hold)替代_范围辅助_来实现地形跟随。
这是因为地形保持使用标准的ECL/EKF估计器来确定高度，在大多数条件下比距离传感器更可靠。
:::

_条件范围辅助_通过设置[EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL) = "启用（条件模式）"（1）来激活。

进一步配置通过`EKF2_RNG_A_`参数：

- [EKF2_RNG_A_VMAX](../advanced_config/parameter_reference.md#EKF2_RNG_A_VMAX)：最大水平速度，超过此值时范围辅助被禁用。
- [EKF2_RNG_A_HMAX](../advanced_config/parameter_reference.md#EKF2_RNG_A_HMAX)：最大高度，超过此值时范围辅助被禁用。
- [EKF2_RNG_A_IGATE](../advanced_config/parameter_reference.md#EKF2_RNG_A_IGATE)：范围辅助一致性检查的"门限"（误差值导致范围辅助被禁用的衡量标准）。

#### 距离高度融合

PX4允许您持续将测距仪作为高度来源（在任何飞行模式/机体类型中）。
这可能对应用在机体仅能保证飞行在近似平坦表面的情况时有用（例如室内环境）。

当使用距离传感器作为高度来源时，飞行人员应注意：

- 飞行经过障碍物时可能导致估算器拒绝测距仪数据（由于内部数据一致性检查），这会导致估算器仅依赖加速度计估计时出现较差的高度保持性能。

  ::: info
  当机体以近似恒定的地面高度上坡飞行时，可能出现这种情况，因为测距仪高度保持不变而加速度计估算的高度发生变化。
  EKF执行创新一致性检查，考虑测量值与当前状态的误差以及状态的估计方差和测量本身的方差。
  如果检查失败，测距仪数据将被拒绝，高度将根据加速度计和其他选定的高度来源（GNSS、气压计、视觉系统）进行估算，前提是这些来源已启用且可用。
  如果不一致数据持续5秒且距离传感器是当前高度数据的活动来源，估算器将重置高度状态以匹配当前距离传感器数据。
  如果存在一个或多个其他高度来源，测距仪将被判定为故障，估算器继续使用其他传感器估算高度。
  测量结果可能会再次变得一致，例如当机体下降或估算高度漂移至与测距仪测量高度匹配时。
  :::

- 本地NED坐标系的原点将随地面高度上下移动。
- 在不平整表面（如树木）上测距仪性能可能非常差，导致数据噪声大且不一致。
  这再次导致较差的高度保持性能。

该功能通过将[EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL)设置为"Enabled"（2）来启用。
要使测距仪在激活时成为高度参考源，请将[EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF)设置为"Range sensor"。

:::tip
若希望仅在无人机静止时启用测距仪融合（以便在起飞和降落时获得更好的高度估计），而其余时间不融合测距仪，可使用[EKF2_RNG_CTRL](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL)的[conditional mode](#条件范围辅助)（1）。
:::

#### 测距仪遮挡检测

EKF可通过运动一致性检查检测测距仪至地面的路径是否被遮挡（例如被载荷遮挡），该检查基于垂直速度估计值与测距仪数据数值导数之间的比较。
如果测距仪与EKF2在统计上不一致，传感器将在剩余飞行中被拒绝使用，除非在至少1秒内以0.5m/s或更高的垂直速度通过统计测试。

该检查仅在测距仪未作为主要高度源时启用，并且仅在机体没有水平移动时激活（假设地面高度为静态）。

为实现有效的遮挡检测，需要使用飞行数据对测距仪噪声参数进行精确调校。
随后可调整运动一致性门限参数以获得所需的故障检测灵敏度。

调校参数：

- [EKF2_RNG_NOISE](../advanced_config/parameter_reference.md#EKF2_RNG_NOISE)
- [EKF2_RNG_K_GATE](../advanced_config/parameter_reference.md#EKF2_RNG_K_GATE)

### 空速

等效空速（EAS）数据可用于通过将[EKF2_ARSP_THR](../advanced_config/parameter_reference.md#EKF2_ARSP_THR)设置为正数来估计风速并减少GPS丢失时的漂移。  
当空速超过[EKF2_ARSP_THR](../advanced_config/parameter_reference.md#EKF2_ARSP_THR)设置的正数值阈值且机体类型不是旋翼时，将使用空速数据。

### 合成侧滑

固定翼平台可以利用假设的侧滑观测值为零来提高风速估计的精度，并且能够在没有空速传感器的情况下实现风速估计。  
通过将参数 [EKF2_FUSE_BETA](../advanced_config/parameter_reference.md#EKF2_FUSE_BETA) 设置为 1 来启用此功能。

<a id="mc_wind_estimation_using_drag"></a>

### 多旋翼基于阻力比力的风速估算

多旋翼平台可以利用机体X/Y轴方向上空速与阻力比力（IMU加速度计测量值）之间的关系，估算北向/东向风速分量。  
可通过[EKF2_DRAG_CTRL](../advanced_config/parameter_reference.md#EKF2_DRAG_CTRL)启用该功能。

X/Y轴方向飞行时的弹道系数（由[EKF2_BCOEF_X](../advanced_config/parameter_reference.md#EKF2_BCOEF_X)、[EKF2_BCOEF_Y](../advanced_config/parameter_reference.md#EKF2_BCOEF_Y)设定），以及旋翼产生的动量阻力（由[EKF2_MCOEF](../advanced_config/parameter_reference.md#EKF2_MCOEF)设定），共同控制空速与比力观测值的关联关系。  
比力观测噪声量由[EKF2_DRAG_NOISE](../advanced_config/parameter_reference.md#EKF2_DRAG_NOISE)参数设定。

良好的参数整定可通过以下步骤实现：

1. 在[Position mode](../flight_modes_mc/position.md)下飞行，重复进行前后/左右/上下方向的加速（从静止到最大速度）测试（在无风条件下测试效果最佳）。
2. 通过[QGroundControl: Analyze > Log Download](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/log_download.html)下载**.ulg**日志文件  
   ::: info  
   同一**.ulg**日志文件也可用于[静态压力位置误差修正系数](#静态压力位置误差校正)的整定。  
   :::
3. 使用日志文件与[mc_wind_estimator_tuning.py](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/ekf2/EKF/python/tuning_tools/mc_wind_estimator) Python脚本获取最优参数集。

### 光流

[Optical flow](../sensor/optical_flow.md)数据将在满足以下条件时使用：

- 可获得有效的测距数据。
- [EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL)已设置。
- 流传感器返回的质量指标大于由[EKF2_OF_QMIN](../advanced_config/parameter_reference.md#EKF2_OF_QMIN)参数设定的最低要求。

为了获得更好的性能，请按照[此处](../sensor/optical_flow.md#ekf2)描述设置光流传感器的位置。

如果在低空（<10米）可以实现稳定悬停，但在更高高度出现缓慢振荡，请考虑调整[optical flow scale factor](../sensor/optical_flow.md#scale-factor)。

### 外部视觉系统

外部视觉系统（例如 Vicon）提供的位置、速度或姿态测量数据可用于状态估计。

可通过设置 [EKF2_EV_CTRL](../advanced_config/parameter_reference.md#EKF2_EV_CTRL) 的相应位为 `true` 来配置融合的测量数据：

- `0`: 水平位置数据
- `1`: 垂直位置数据  
  额外的高度参考可通过 [EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF) 进行配置（详见 [高度](#高度) 章节）
- `2`: 速度数据
- `3`: 偏航角数据

注意：当使用偏航角数据（位3）时，航向角是相对于外部视觉坐标系的；否则航向角是相对于正北方向的。

EKF 会考虑视觉姿态估计中的不确定性。
这些不确定性信息可通过 MAVLink [ODOMETRY](https://mavlink.io/en/messages/common.html#ODOMETRY) 消息中的协方差字段传递，也可以通过参数 [EKF2_EVP_NOISE](../advanced_config/parameter_reference.md#EKF2_EVP_NOISE)、[EKF2_EVV_NOISE](../advanced_config/parameter_reference.md#EKF2_EVV_NOISE) 和 [EKF2_EVA_NOISE](../advanced_config/parameter_reference.md#EKF2_EVA_NOISE) 进行设置。
可通过 [EKF2_EV_NOISE_MD](../advanced_config/parameter_reference.md#EKF2_EV_NOISE_MD) 参数选择不确定性来源。

## 如何使用 'ecl' 库 EKF？

EKF2 默认启用（如需更多信息请参见 [切换状态估计器](../advanced/switching_state_estimators.md) 和 [EKF2_EN](../advanced_config/parameter_reference.md#EKF2_EN)）。

## ecl EKF相对于其他估计器的优缺点是什么？

与所有估计器一样，性能很大程度上取决于调参以匹配传感器特性。调参是在准确性和鲁棒性之间的权衡，尽管我们尝试提供满足大多数用户需求的调参，但在某些应用中仍需要调整参数。

因此，我们未对传统组合`attitude_estimator_q` + `local_position_estimator`的准确性做出比较，最佳估计器的选择将取决于具体应用场景和调参需求。

### 缺点

- ecl EKF 是一个复杂的算法，要成功调整需要对扩展卡尔曼滤波理论及其在导航问题中的应用有良好的理解。
  因此对于未能取得理想效果的用户来说，更难以判断需要进行哪些调整。
- ecl EKF 会占用更多的 RAM 和 flash 空间。
- ecl EKF 会占用更多的日志空间。

### 优势

- ecl EKF 能够以数学一致性的方式融合来自具有不同时间延迟和数据速率的传感器数据，这在动态操作中（一旦时间延迟参数设置正确）可提高精度。
- ecl EKF 能够融合多种类型的传感器数据。
- ecl EKF 可检测并报告传感器数据中具有统计学意义的不一致性，有助于诊断传感器错误。
- 对于固定翼飞行，ecl EKF 可在有或无空速传感器的情况下估算风速，并能结合估算的风速、空速测量值和侧滑假设，在飞行中 GPS 信号丢失时延长航迹推算时间。
- ecl EKF 估算三轴加速度计偏差，这对尾座式飞行器和其他在飞行阶段间经历大幅姿态变化的机体可提高精度。
- 联邦架构（融合姿态与位置/速度估算）意味着姿态估算可受益于所有传感器测量数据。若正确调校，这应能实现更优的姿态估算。

## 如何检查 EKF 性能？

EKF 输出、状态和状态数据会发布到多个 uORB 主题，并在飞行期间记录到 SD 卡中。  
本指南假设已使用 _**.ulog** 文件格式_ 进行了数据记录。  
**.ulog** 格式的数据可以通过 [PX4 pyulog library](https://github.com/PX4/pyulog) 库在 Python 中进行解析。

大多数 EKF 数据包含在 [EstimatorInnovations](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg) 和 [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg) uORB 消息中，这些消息会被记录到 .ulog 文件中。

一个可自动生成分析图表和元数据的 Python 脚本可在此处找到 [here](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/process_logdata_ekf.py)。  
要使用此脚本文件，请进入 `Tools/ecl_ekf` 目录并输入 `python process_logdata_ekf.py <log_file.ulg>`。  
这会将性能元数据保存到名为 **<log_file>.mdat.csv** 的 CSV 文件中，并将图表保存到名为 `<log_file>.pdf` 的 PDF 文件中。

目录中的多个日志文件可以通过 [batch_process_logdata_ekf.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/batch_process_logdata_ekf.py) 脚本进行分析。  
执行完成后，可以使用 [batch_process_metadata_ekf.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/batch_process_metadata_ekf.py) 脚本对性能元数据文件进行处理，以提供对日志集估算器性能的统计评估。

### 输出数据

- 姿态输出数据位于 [机体姿态](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAttitude.msg) 消息中。
- 局部位置输出数据位于 [机体局部位置](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleLocalPosition.msg) 消息中。
- 全局（WGS-84）输出数据位于 [机体全局位置](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleGlobalPosition.msg) 消息中。
- 风速输出数据位于 [Wind.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Wind.msg) 消息中。

### 状态

请参考[EstimatorStates](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStates.msg)中的状态[24]。  
状态[24]的索引映射如下：

- [0 ... 3] 四元数
- [4 ... 6] 速度 NED (m/s)
- [7 ... 9] 位置 NED (m)
- [10 ... 12] IMU 角度偏差 XYZ (rad)
- [13 ... 15] IMU 速度偏差 XYZ (m/s)
- [16 ... 18] 地磁场 NED (gauss)
- [19 ... 21] 机体磁场 XYZ (gauss)
- [22 ... 23] 风速 NE (m/s)

### 状态方差

参考 [EstimatorStates](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStates.msg) 中的协方差[24]。
协方差[24]的索引映射如下：

- [0 ... 3] 四元数
- [4 ... 6] 速度 NED (m/s)^2
- [7 ... 9] 位置 NED (m^2)
- [10 ... 12] IMU 角增量偏差 XYZ (rad^2)
- [13 ... 15] IMU 速度增量偏差 XYZ (m/s)^2
- [16 ... 18] 地球磁场 NED (gauss^2)
- [19 ... 21] 机体磁场 XYZ (gauss^2)
- [22 ... 23] 风速 NE (m/s)^2

### 观测创新与创新方差

观测 `estimator_innovations`、`estimator_innovation_variances` 和 `estimator_innovation_test_ratios` 消息字段定义在[EstimatorInnovations.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)中。  
这些消息具有相同的字段名/类型（但单位不同）。

::: info  
这些消息具有相同的字段是因为它们来自相同的字段定义。  
文件末尾的 `# TOPICS` 行（[查看文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)）列出了要创建的消息集合名称）：  

```  

```

# 主题 estimator_innovations estimator_innovation_variances estimator_innovation_test_ratios

```

:::

部分观测内容包括：

- 磁力计 XYZ（高斯，高斯²）：`mag_field[3]`
- 偏航角（弧度，弧度²）：`heading`
- 真实空速（米/秒，（米/秒）²）：`airspeed`
- 合成侧滑（弧度，弧度²）：`beta`
- 光流 XY（弧度/秒，（弧度/秒）²）：`flow`
- 地面高度（米，米²）：`hagl`
- 阻力比力（（米/秒）²）：`drag`
- 速度与位置观测值：按传感器划分

此外，每个传感器都有自己的水平和垂直位置和/或速度值字段（适用时）。
这些字段基本具有自描述特性，如下所示：

# GPS
float32[2] gps_hvel	# 水平GPS速度创新 (m/sec) 和创新方差 ((m/sec)**2)
float32    gps_vvel	# 垂直GPS速度创新 (m/sec) 和创新方差 ((m/sec)**2)
float32[2] gps_hpos	# 水平GPS位置创新 (m) 和创新方差 (m**2)
float32    gps_vpos	# 垂直GPS位置创新 (m) 和创新方差 (m**2)

# 外部视觉  
float32[2] ev_hvel # 水平外部视觉速度创新值（m/sec）和创新方差（(m/sec)**2）  
float32    ev_vvel # 垂直外部视觉速度创新值（m/sec）和创新方差（(m/sec)**2）  
float32[2] ev_hpos # 水平外部视觉位置创新值（m）和创新方差（m**2）  
float32    ev_vpos # 垂直外部视觉位置创新值（m）和创新方差（m**2）

# 假位置和速度
float32[2] fake_hvel	# 假水平速度创新（m/s）和创新方差（(m/s)**2）
float32    fake_vvel	# 假垂直速度创新（m/s）和创新方差（(m/s)**2）
float32[2] fake_hpos	# 假水平位置创新（m）和创新方差（m**2）
float32    fake_vpos	# 假垂直位置创新（m）和创新方差（m**2）

# 高度传感器
float32 rng_vpos	# 测距传感器高度创新 (m) 和创新方差 (m**2)
float32 baro_vpos	# 气压计高度创新 (m) 和创新方差 (m**2)

# 辅助速度
float32[2] aux_hvel	# 水平辅助速度创新值来自着陆目标测量 (m/sec) 和创新方差 ((m/sec)**2)
float32    aux_vvel	# 垂直辅助速度创新值来自着陆目标测量 (m/sec) 和创新方差 ((m/sec)**2)
```

### 输出互补滤波器

输出互补滤波器用于从融合时间范围向前传播状态到当前时间。  
要检查在融合时间范围测量的角度、速度和位置跟踪误差的幅度，请参考`ekf2_innovations`消息中的`output_tracking_error[3]`。

索引映射如下：

- [0] 角度跟踪误差幅度 (rad)
- [1] 速度跟踪误差幅度 (m/s)。  
  速度跟踪时间常数可通过 [EKF2_TAU_VEL](../advanced_config/parameter_reference.md#EKF2_TAU_VEL) 参数进行调整。  
  减小此参数会减少稳态误差，但会增加NED速度输出的观测噪声。
- [2] 位置跟踪误差幅度 $m$。  
  位置跟踪时间常数可通过 [EKF2_TAU_POS](../advanced_config/parameter_reference.md#EKF2_TAU_POS) 参数进行调整。  
  减小此参数会减少稳态误差，但会增加NED位置输出的观测噪声。

### EKF错误

EKF包含针对状态和协方差更新异常情况的内部错误检查机制。
请参考 [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg) 中的 `filter_fault_flags`。

### 观测错误

观测故障分为两类：

- 数据丢失。
  例如距离传感器未能提供返回值。
- 创新值（innovation）过大，即状态预测与传感器观测值之间的差异过大。
  例如剧烈振动导致垂直位置误差过大，从而导致气压计高度测量被拒绝。

这两种情况都可能导致观测数据被拒绝的时间足够长，从而促使EKF尝试使用传感器观测值重置状态。
所有观测值的创新值都会进行统计置信度检查。
每种观测类型的检查标准差数量由参数`EKF2_*_GATE`控制。

在[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中可获取测试等级如下：

- `mag_test_ratio`：最大磁力计创新值分量与创新值测试限制的比值
- `vel_test_ratio`：最大速度创新值分量与创新值测试限制的比值
- `pos_test_ratio`：最大水平位置创新值分量与创新值测试限制的比值
- `hgt_test_ratio`：垂直位置创新值与创新值测试限制的比值
- `tas_test_ratio`：真实空速创新值与创新值测试限制的比值
- `hagl_test_ratio`：地高创新值与创新值测试限制的比值

关于每个传感器的二元通过/失败摘要，请参考[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中的`innovation_check_flags`。

### GPS质量检查

EKF在开始GPS辅助之前会执行一系列GPS质量检查。  
这些检查由参数[EKF2_GPS_CHECK](../advanced_config/parameter_reference.md#EKF2_GPS_CHECK)和`EKF2_REQ_*`控制。  
这些检查的通过/失败状态会记录在[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).gps_check_fail_flags消息中。  
当所有必需的GPS检查都通过时，该整数值将为零。  
如果EKF未开始GPS对齐，请将该整数值与[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中`gps_check_fail_flags`的位掩码定义进行比对。

### EKF数值错误

EKF在所有计算中使用单精度浮点运算，并采用一阶近似推导协方差预测和更新方程，以减少处理需求。这意味着在重新调整EKF时，可能会遇到协方差矩阵操作条件恶化到足以导致发散或状态估计出现显著误差的情况。

为防止这种情况，每个协方差和状态更新步骤都包含以下错误检测和纠正步骤：

- 如果创新方差小于观测方差（这需要状态方差为负，这是不可能的），或者协方差更新会导致任何状态的方差为负，则：
  - 跳过状态和协方差更新
  - 重置协方差矩阵中对应行和列
  - 将故障记录在[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg) `filter_fault_flags`消息中
- 状态方差（协方差矩阵的对角线）被约束为非负数
- 对状态方差应用上限限制
- 强制协方差矩阵的对称性

重新调整滤波器后，特别是涉及降低噪声变量的调整，应检查`estimator_status.gps_check_fail_flags`的值，确保其保持为零。

## 如果高度估计出现发散我该怎么办？

EKF高度估计在飞行过程中偏离GPS和气压计测量值最常见的原因是振动导致IMU测量值的截断和/或混叠。
如果出现这种情况，数据中应能明显观察到以下现象：

- [EstimatorInnovations](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg).vel_pos_innov[2] 和 [EstimatorInnovations](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg).vel_pos_innov[5] 将具有相同的符号。
- [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).hgt_test_ratio 将大于1.0

建议的首要步骤是确保飞控系统通过有效的隔离安装系统与机体分离。
隔离支架具有6个自由度，因此有6个共振频率。
通常规则要求，安装在隔离支架上的飞控系统6个共振频率应高于25Hz以避免与飞控动力学交互，同时低于电机频率。

如果共振频率与电机或螺旋桨叶片通过频率重合，隔离支架可能会加剧振动。

可通过以下参数调整使EKF更抵抗振动引起的高度发散：

- 将主高度传感器的创新门限值加倍。
  如果使用气压高度，这是 [EKF2_BARO_GATE](../advanced_config/parameter_reference.md#EKF2_BARO_GATE)。
- 初始时将 [EKF2_ACC_NOISE](../advanced_config/parameter_reference.md#EKF2_ACC_NOISE) 值增加到0.5。
  如果发散仍然发生，可进一步每次增加0.1但不超过1.0

请注意，这些更改会使EKF对GPS垂直速度和气压计误差更加敏感。

## 如果位置估计发散，我该怎么办？

位置估计发散的最常见原因包括：

- 高振动水平。
  - 通过改进飞控的机械隔离来修复。
  - 增大 [EKF2_ACC_NOISE](../advanced_config/parameter_reference.md#EKF2_ACC_NOISE) 和 [EKF2_GYR_NOISE](../advanced_config/parameter_reference.md#EKF2_GYR_NOISE) 的值可能有所帮助，但这会使EKF更容易受到GPS干扰的影响。
- 大量陀螺仪偏置偏差。
  - 通过重新校准陀螺仪修复。
    检查是否过度温度敏感（冷启动后预热期间偏置变化超过3度/秒），受影响时应更换传感器或增加隔热以减缓温度变化速率。
- 偏航对齐错误
  - 检查磁力计校准和对齐。
  - 检查QGC显示的航向是否在真实值15度以内
- GPS精度差
  - 检查是否存在干扰
  - 改善分离度和屏蔽效果
  - 检查飞行区域是否存在GPS信号遮挡和反射源（附近高层建筑）
- GPS信号丢失

确定主要原因需要系统性地分析EKF日志数据：

- 绘制速度创新测试比 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vel_test_ratio
- 绘制水平位置创新测试比 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).pos_test_ratio
- 绘制高度创新测试比 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).hgt_test_ratio
- 绘制磁力计创新测试比 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).mag_test_ratio
- 绘制GPS接收器报告的速度精度 - [SensorGps.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGps.msg).s_variance_m_s
- 绘制IMU delta角度状态估计 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).states[10]、states[11] 和 states[12]
- 绘制EKF内部高频振动指标：
  - Delta角度锥形振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[0]
  - 高频delta角度振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[1]
  - 高频delta速度振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[2]

在正常运行期间，所有测试比值应保持在0.5以下，偶尔会出现短暂的高于该值的峰值，如下图成功飞行示例所示：

![位置、速度、高度和磁力计测试比值](../../assets/ecl/test_ratios_-_successful.png)

下图显示了隔离性能良好的多旋翼的EKF振动指标。
可以观察到着陆冲击及起降时振动增加。
目前通过这些指标收集的数据不足以提供具体的最大阈值建议。

![振动指标 - 成功](../../assets/ecl/vibration_metrics_-_successful.png)

上述振动指标存在一定局限性，因为当振动频率接近IMU采样频率（大多数板载1kHz）时，会导致数据中出现偏移，这些偏移不会反映在高频振动指标中。
检测混叠误差的唯一方法是观察其对惯性导航精度的影响以及创新水平的上升。

除了产生大于1.0的位置和速度测试比值外，不同的错误机制还会影响其他测试比值的方式各不相同：

### 过度振动的判定

高振动水平通常会影响垂直位置和速度的更新以及水平分量。  
磁力计测试水平仅受到轻微影响。  

$insert example plots showing bad vibration here$

### 过大陀螺仪偏置的判定

过大的陀螺仪偏置通常表现为飞行过程中delta angle bias值变化超过5E-4（相当于约3度/秒），并且如果偏航轴受影响，还会导致磁强计测试比率显著增加。  
高度通常不会受影响，除非出现极端情况。  
开机偏置值高达5度/秒是可以容忍的，前提是飞行前滤波器有足够的时间稳定。  
指挥器执行的飞行前检查应防止启动，如果位置发散。

$insert example plots showing bad gyro bias here$

### 偏航精度不佳的判定

偏航校准不良会导致机体开始移动时速度测试比率迅速增加，这是由于惯性导航计算的速度方向与GPS测量结果存在不一致。
磁力计的创新值受到轻微影响。
高度通常不受影响。

（插入展示偏航校准不良的示例图）

### GPS精度差的判定

GPS精度差通常伴随着接收机报告的速度误差升高，同时创新值也会升高。由于多路径传播、遮挡和干扰导致的瞬时误差更为常见。以下是一个GPS精度暂时丢失的示例，多旋翼无人机开始偏离其盘旋位置，必须通过操纵杆进行修正。[估计器状态](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vel_test_ratio升高至1以上表明GPS速度与其他测量值不一致，已被拒绝。

![GPS故障 - 测试比率](../../assets/ecl/gps_glitch_-_test_ratios.png)

这伴随着GPS接收机报告的速度精度升高，表明很可能是GPS误差。

![GPS故障 - 报告的接收机精度](../../assets/ecl/gps_glitch_-_reported_receiver_accuracy.png)

如果我们再查看GPS水平速度创新值和创新方差，可以看到伴随此次GPS“故障”事件的北向速度创新值大幅升高。

![GPS故障 - 速度创新值](../../assets/ecl/gps_glitch_-_velocity_innovations.png)

### GPS数据丢失的判定

GPS数据丢失将通过速度和位置创新测试比值的"直线化"显示。  
如果发生这种情况，请检查`vehicle_gps_position`中的其他GPS状态数据以获取更多信息。

下图展示了使用SITL Gazebo模拟的VTOL飞行中生成的NED GPS速度创新`ekf2_innovations_0.vel_pos_innov[0 ... 2]`，GPS NE位置创新`ekf2_innovations_0.vel_pos_innov[3 ... 4]`和气压计垂直位置创新`ekf2_innovations_0.vel_pos_innov[5]`。

模拟的GPS在73秒时被设定为失锁。  
注意GPS丢失后NED速度创新和NE位置创新开始"直线化"。  
请注意，在缺少GPS数据10秒后，EKF会回到使用最后已知位置的静态位置模式，NE位置创新开始再次变化。

![GPS数据丢失 - 在SITL中](../../assets/ecl/gps_data_loss_-_velocity_innovations.png)

### 气压计地面效应补偿

如果机体在着陆时出现接近地面时自动抬高的现象，最可能的原因是气压计地面效应。

这是由螺旋桨向下推动的气流撞击地面后形成高压区域造成的。
结果是气压高度测量值降低，导致系统发出不必要的爬升指令。
下图展示了地面效应的典型场景。
请注意气压计信号在飞行起始和结束阶段的下降情况。

![气压计地面效应](../../assets/ecl/gnd_effect.png)

可通过启用_地面效应补偿_来解决此问题：

- 从曲线图中估算起降阶段气压计下降的幅度。在上图中可读取到着陆时气压计下降约6米。
- 然后将参数 [EKF2_GND_EFF_DZ](../advanced_config/parameter_reference.md#EKF2_GND_EFF_DZ) 设置为该值并增加10%的余量。因此在此示例中，6.6米是一个良好的初始值。

如果可用地形估计（例如机体配备测距仪），还可以指定参数 [EKF2_GND_MAX_HGT](../advanced_config/parameter_reference.md#EKF2_GND_MAX_HGT)，表示应启用地面效应补偿的离地高度范围。
若无可用地形估计，该参数将无效，系统将使用启发式方法判断是否需要启用地面效应补偿。

## 更多信息

- [PX4状态估计概述](https://youtu.be/HkYRJJoyBwQ), _PX4开发者峰会2019_, Dr. Paul Riseborough): 估计器概述，以及2018/19年的重要变更，以及预期在2019/20年期间的改进。