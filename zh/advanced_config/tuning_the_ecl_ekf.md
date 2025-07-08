# 使用 PX4 的导航滤波器（EKF2）

本教程解答了关于导航中使用的 EKF 算法的常见问题。

:::tip
[PX4 状态估计概述](https://youtu.be/HkYRJJoyBwQ) 视频来自 _PX4 开发者峰会 2019_（Paul Riseborough 博士），该视频概述了估计器，并进一步描述了 2018/2019 年间的主要变更，以及此后添加的重大改进。
:::

## 概述

PX4的导航滤波器使用扩展卡尔曼滤波器（Extended Kalman Filter，EKF）算法处理传感器测量数据，提供以下状态的估计值：

- 从北-东-下（NED）局部导航坐标系到机体X-Y-Z坐标系的旋转四元数  
- 惯性测量单元（IMU）位置的三维速度 - 北向、东向、下向（m/s）  
- IMU位置的三维坐标 - 纬度（rad）、经度（rad）、海拔高度（m）  
- IMU陀螺仪偏差估计 - X、Y、Z（rad/s）  
- IMU加速度计偏差估计 - X、Y、Z（m/s<sup>2</sup>）  
- 地磁场分量 - 北向、东向、下向（gauss）  
- 机体坐标系磁场偏差 - X、Y、Z（gauss）  
- 风速 - 北向、东向（m/s）  
- 地形高度（m）

为提高稳定性，实现了"误差状态"公式  
这在估计旋转的不确定性（SO(3)的切空间3D向量）时尤为重要。

EKF在延迟的"融合时间范围"上运行，以允许不同传感器相对于IMU的时间延迟。  
每个传感器的数据通过FIFO缓冲存储，并由EKF在正确时间从缓冲区中取出使用。  
每个传感器的延迟补偿由[EKF2\_\*\_DELAY](../advanced_config/parameter_reference.md#ekf2)参数控制。

使用互补滤波器通过缓冲的IMU数据，将状态从"融合时间范围"向前传播到当前时间。  
该滤波器的时间常数由[EKF2_TAU_VEL](../advanced_config/parameter_reference.md#EK2_TAU_VEL)和[EKF2_TAU_POS](../advanced_config/parameter_reference.md#EKF2_TAU_POS)参数控制。

::: info  
"融合时间范围"的延迟和缓冲区长度由[EKF2_DELAY_MAX](../advanced_config/parameter_reference.md#EKF2_DELAY_MAX)确定。  
该值应至少等于最长延迟`EKF2\_\*\_DELAY`。  
减少"融合时间范围"延迟可降低用于将状态向前传播到当前时间的互补滤波器的误差。  
:::

EKF仅使用IMU数据进行状态预测。  
IMU数据不会作为观察值用于EKF推导。  
协方差预测和测量雅可比矩阵的代数方程通过[SymForce](https://symforce.org/)推导，代码位于此处：[Symbolic Derivation](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/ekf2/EKF/python/ekf_derivation/derivation.py)。  
协方差更新使用[Joseph Stabilized form](https://en.wikipedia.org/wiki/Kalman_filter#Deriving_the_posteriori_estimate_covariance_matrix)以提高数值稳定性，并允许独立状态的条件更新。

### 关于位置输出的精度说明

位置估计为纬度、经度和海拔高度，惯性导航系统（INS）集成使用WGS84椭球模式进行计算。  
然而，位置不确定性是在当前位置局部导航坐标系中定义的（即：NED误差，单位为米）。

位置和速度状态在输出到控制回路前，会根据IMU与机体坐标系之间的偏移量进行调整。  
IMU相对于机体坐标系的位置由`EKF2_IMU_POS_X,Y,Z`参数设置。

除了全局位置估计（纬度/经度/海拔高度），滤波器还通过使用以任意原点为中心的[azimuthal_equidistant_projection](https://en.wikipedia.org/wiki/Azimuthal_equidistant_projection)将全局位置估计投影到局部坐标系，提供局部位置估计（NED，单位为米）。  
此原点在融合全局位置测量时会自动设置，也可以手动指定。  
如果没有提供全局位置信息，则仅可用局部位置，此时INS集成基于球形地球模型进行。

## 运行单个EKF实例

默认行为是运行单个EKF实例。在这种情况下，传感器选择和故障转移会在数据传送到EKF之前完成。这可以应对有限数量的传感器故障（如数据丢失），但无法应对传感器提供超出EKF和控制回路补偿能力的不准确数据。

运行单个EKF实例的参数设置为：

- [EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) = 0
- [EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) = 0
- [SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE) = 1
- [SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE) = 1

## 运行多个EKF实例

根据惯性测量单元（IMU）和磁强计的数量以及自动驾驶仪的CPU容量，可以运行多个EKF实例。  
这提供了更广泛的传感器错误保护，其实现方式是每个EKF实例使用不同的传感器组合。  
通过比较每个EKF实例的内部一致性，EKF选择器能够确定数据一致性最佳的EKF和传感器组合。  
这使得能够检测和隔离诸如IMU偏置突然变化、饱和或卡死数据等故障。

EKF实例的总数是IMU数量与[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU)和磁强计数量与[EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG)的乘积，计算公式如下：

> N_instances = MAX([EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) , 1) x MAX([EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) , 1)

例如，具有2个IMU和2个磁强计的自动驾驶仪可通过设置EKF2_MULTI_IMU = 2和EKF2_MULTI_MAG = 2运行4个EKF实例，每个实例使用以下传感器组合：

- EKF实例1 : IMU 1, 磁强计 1  
- EKF实例2 : IMU 1, 磁强计 2  
- EKF实例3 : IMU 2, 磁强计 1  
- EKF实例4 : IMU 2, 磁强计 2  

理论上最多可处理4个IMU和4个磁强计传感器，形成4 x 4 = 16个EKF实例。  
实际中受可用计算资源限制。在基于STM32F7 CPU的硬件开发测试中，4个EKF实例的处理负载和内存占用率在可接受范围内。

:::warning
飞行前应进行地面测试以检查CPU和内存使用情况。
:::

如果[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) >= 3，则大速率陀螺仪误差的故障切换时间会进一步缩短，因为EKF选择器可以应用中值选择策略以更快隔离故障IMU。

多个EKF实例的配置由以下参数控制：

- [SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE):  
  如果运行具有IMU传感器多样性的多个EKF实例（即[EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU) > 1），则设置为0。  

  当设置为1（单EKF操作的默认值）时，传感器模块会选择EKF使用的IMU数据。  
  这可以防止传感器数据丢失，但无法防止坏数据。  
  当设置为0时，传感器模块不进行选择。

- [SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE):  
  如果运行具有磁强计传感器多样性的多个EKF实例（即[EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG) > 1），则设置为0。  

  当设置为1（单EKF操作的默认值）时，传感器模块会选择EKF使用的磁强计数据。  
  这可以防止传感器数据丢失，但无法防止坏数据。  
  当设置为0时，传感器模块不进行选择。

- [EKF2_MULTI_IMU](../advanced_config/parameter_reference.md#EKF2_MULTI_IMU):  
  该参数指定多个EKF使用的IMU传感器数量。  
  如果`EKF2_MULTI_IMU` <= 1，则仅使用第一个IMU传感器。  
  当[SENS_IMU_MODE](../advanced_config/parameter_reference.md#SENS_IMU_MODE) = 1时，这将是传感器模块选择的传感器。  
  如果`EKF2_MULTI_IMU` >= 2，则会为指定数量的IMU传感器单独运行EKF实例，最多为4个或现有IMU数量中的较小值。

- [EKF2_MULTI_MAG](../advanced_config/parameter_reference.md#EKF2_MULTI_MAG):  
  该参数指定多个EKF使用的磁强计传感器数量。  
  如果`EKF2_MULTI_MAG` <= 1，则仅使用第一个磁强计传感器。  
  当[SENS_MAG_MODE](../advanced_config/parameter_reference.md#SENS_MAG_MODE) = 1时，这将是传感器模块选择的传感器。  
  如果`EKF2_MULTI_MAG` >= 2，则会为指定数量的磁强计传感器单独运行EKF实例，最多为4个或现有磁强计数量中的较小值。

::: info
不支持带有多个EKF实例的飞行日志记录和[EKF2回放](../debug/system_wide_replay.md#ekf2-replay)。  
要启用EKF回放的记录功能，必须设置参数以启用[单个EKF实例](#running-a-single-ekf-instance)。
:::以下是关于PX4飞控系统EKF2模块中传感器融合技术的中文解释，涵盖GPS、测距传感器、空速计、光流传感器、外部视觉系统等关键内容：

---

### **GPS数据融合**
- **双GPS系统**：通过`EKF2_GPS_PRIMARY`参数选择主GPS（默认为0）。当两个GPS的更新间隔相差1秒以上时，系统会切换到备用GPS。
- **数据混合**：若`EKF2_GPS_BLEND`设为1，系统会根据高度和速度动态混合两组GPS数据，避免位置突变。混合阈值由`EKF2_GPS_BLEND_H`（高度）和`EKF2_GPS_BLEND_V`（速度）参数控制。

---

### **测距传感器融合**
#### **条件融合模式（EKF2_RNG_CTRL=1）**
- **适用场景**：仅在低速（低于`EKF2_RNG_A_VMAX`）和低高度（低于`EKF2_RNG_A_HMAX`）时启用，适合起降阶段。
- **一致性检查**：通过`EKF2_RNG_A_IGATE`参数控制数据一致性阈值，避免异常值干扰。

#### **连续融合模式（EKF2_RNG_CTRL=2）**
- **适用场景**：适用于室内等平坦表面飞行。需设置`EKF2_HGT_REF=2`将测距传感器作为高度参考。
- **注意事项**：
  - 飞越障碍物可能导致数据被拒绝（EKF2通过误差检查判断不一致）。
  - 地面起伏会影响NED坐标系的原点稳定性。
  - 不规则表面（如树木）会导致数据噪声增加。

#### **障碍物检测**
- **检测原理**：通过比较垂直速度估计值与测距传感器数据的数值导数，判断路径是否被遮挡。
- **参数调参**：
  - `EKF2_RNG_NOISE`：调整传感器噪声模型。
  - `EKF2_RNG_K_GATE`：控制检测灵敏度。

---

### **空速传感器**
- **风速估计**：设置`EKF2_ARSP_THR`为正数，当空速超过阈值时启用风速估计，减少GPS失效时的漂移。

---

### **合成侧滑角（固定翼）**
- **功能**：假设侧滑角为零，提升固定翼风速估计精度。通过`EKF2_FUSE_BETA=1`启用。

---

### **多旋翼风速估计（基于阻力）**
- **原理**：利用X/Y轴方向的气动阻力与加速度计数据的关系估计风速。
- **参数调参**：
  - `EKF2_BCOEF_X/Y`：X/Y轴方向的弹道系数。
  - `EKF2_MCOEF`：螺旋桨动量阻力系数。
  - `EKF2_DRAG_NOISE`：观测噪声参数。
- **调校步骤**：
  1. 在无风条件下进行位置模式飞行，测试最大速度和悬停。
  2. 使用`.ulg`日志文件和`mc_wind_estimator_tuning.py`脚本优化参数。

---

### **光流传感器**
- **启用条件**：
  - 有效测距数据。
  - `EKF2_OF_CTRL`启用。
  - 光流传感器质量指标`EKF2_OF_QMIN`满足要求。
- **性能优化**：高海拔悬停不稳定时，调整光流比例因子（见光流传感器配置）。

---

### **外部视觉系统**
- **融合配置**：通过`EKF2_EV_CTRL`参数选择融合位置（0-1: 水平/垂直位置，2: 速度，3: 偏航角）。
- **不确定性处理**：
  - 通过MAVLink的`ODOMETRY`消息协方差字段传递。
  - 或通过`EKF2_EVP_NOISE`（位置）、`EKF2_EVV_NOISE`（速度）、`EKF2_EVA_NOISE`（姿态）参数设置。
- **参考系说明**：若使用偏航角（bit3），航向基于外部视觉坐标系；否则为磁北方向。

---

### **关键参数表**
| 参数 | 功能 | 默认值 |
|------|------|--------|
| `EKF2_GPS_BLEND` | GPS数据混合模式 | 0（关闭） |
| `EKF2_RNG_CTRL` | 测距融合模式 | 0（关闭） |
| `EKF2_HGT_REF` | 高度参考源 | 1（GPS） |
| `EKF2_ARSP_THR` | 空速阈值 | 0（禁用） |
| `EKF2_FUSE_BETA` | 合成侧滑角 | 0（禁用） |
| `EKF2_DRAG_CTRL` | 多旋翼风速估计 | 0（禁用） |
| `EKF2_OF_CTRL` | 光流融合 | 0（禁用） |
| `EKF2_EV_CTRL` | 外部视觉融合 | 0（无融合） |

---

### **注意事项**
- **传感器冲突处理**：EKF2会根据一致性检查动态启用/禁用传感器，避免多源数据冲突。
- **日志分析**：使用QGroundControl下载`.ulg`日志文件，结合Python脚本进行参数调优（如`mc_wind_estimator_tuning.py`）。
- **安全提示**：测距传感器在非平坦地面或复杂环境中可能导致估计误差，需结合其他传感器（如GNSS）进行冗余校验。

---

通过合理配置上述参数和传感器，可显著提升飞行器在复杂环境下的定位精度和稳定性。具体调参建议结合实际飞行测试和日志分析进行优化。

## 如何使用 'ecl' 库中的EKF？

EKF2默认启用（更多信息请参见[切换状态估计器](../advanced/switching_state_estimators.md)和[EKF2_EN](../advanced_config/parameter_reference.md#EKF2_EN)）。

## ecl EKF相比其他估计算法的优势和劣势是什么？

与所有估计算法一样，性能的大部分来源于针对传感器特性的调参。调参是在精度和鲁棒性之间进行的权衡，虽然我们已尝试提供满足大多数用户需求的调参方案，但在某些应用中仍需进行调参修改。

因此，我们并未声称ecl EKF相对于`attitude_estimator_q` + `local_position_estimator`的传统组合具有更高的精度，最佳估计算法的选择将取决于具体应用场景和调参情况。

### 劣势

- ecl EKF是一个复杂的算法，成功调参需要深入理解扩展卡尔曼滤波理论及其在导航问题中的应用。这使得对于未能获得良好结果的用户来说，难以判断应该如何调整参数。
- ecl EKF占用更多的RAM和闪存空间。
- ecl EKF占用更多的日志存储空间。

### 优势

- ecl EKF能够以数学上的一致方式融合来自具有不同时间延迟和数据率的传感器数据，这在正确设置时间延迟参数后，可以提升动态操作期间的精度。
- ecl EKF能够融合多种不同类型的传感器。
- ecl EKF检测并报告传感器数据中统计上显著的不一致，这有助于诊断传感器错误。
- 对于固定翼飞行，ecl EKF能够在有或无空速传感器的情况下估算风速，并能够结合估算的风速、空速测量值和侧滑假设来延长在飞行中GPS丢失时的航位推算时间。
- ecl EKF估算三轴加速度计偏置，这对在飞行阶段间经历大幅姿态变化的尾座机和其他机体的精度提升有帮助。
- 联邦架构（结合姿态与位置/速度估计）意味着姿态估计能够利用所有传感器的测量数据。如果调参得当，这应能带来更优的姿态估计效果。

## 如何检查EKF性能？

EKF输出、状态和状态数据会发布到多个uORB主题，并在飞行期间记录到SD卡中。本指南假设数据已使用_.ulog文件格式_进行记录。**.ulog**格式数据可通过使用[PX4 pyulog库](https://github.com/PX4/pyulog)在Python中解析。

大部分EKF数据包含在[EKF创新数据](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)和[EKF状态数据](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)的uORB消息中，这些消息会被记录到.ulog文件中。

一个可自动生成分析图表和元数据的Python脚本可在此找到：[process_logdata_ekf.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/process_logdata_ekf.py)。使用该脚本时，请进入`Tools/ecl_ekf`目录并输入`python process_logdata_ekf.py <log_file.ulg>`。这将生成名为**<log_file>.mdat.csv**的性能元数据文件和名为`<log_file>.pdf`的PDF图表文件。

可通过[batch_process_logdata_ekf.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/batch_process_logdata_ekf.py)脚本批量分析目录中的多个日志文件。完成此操作后，可使用[batch_process_metadata_ekf.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/ecl_ekf/batch_process_metadata_ekf.py)脚本处理元数据文件，对日志集的估计器性能进行统计评估。

### 输出数据

- 姿态输出数据位于[VehicleAttitude](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAttitude.msg)消息中。
- 本体位置输出数据位于[VehicleLocalPosition](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleLocalPosition.msg)消息中。
- 全球(WGS-84)输出数据位于[VehicleGlobalPosition](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleGlobalPosition.msg)消息中。
- 风速输出数据位于[Wind.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Wind.msg)消息中。

### 状态数据

请参阅[EstimatorStates](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStates.msg)中的states[24]。states[24]的索引映射如下：

- [0 ... 3] 四元数
- [4 ... 6] 速度NED (m/s)
- [7 ... 9] 位置NED (m)
- [10 ... 12] IMU角度偏差XYZ (rad)
- [13 ... 15] IMU速度偏差XYZ (m/s)
- [16 ... 18] 地球磁场NED (gauss)
- [19 ... 21] 机体磁场XYZ (gauss)
- [22 ... 23] 风速NE (m/s)

### 状态方差

请参阅[EstimatorStates](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStates.msg)中的covariances[24]。covariances[24]的索引映射如下：

- [0 ... 3] 四元数
- [4 ... 6] 速度NED (m/s)^2
- [7 ... 9] 位置NED (m^2)
- [10 ... 12] IMU角度偏差XYZ (rad^2)
- [13 ... 15] IMU速度偏差XYZ (m/s)^2
- [16 ... 18] 地球磁场NED (gauss^2)
- [19 ... 21] 机体磁场XYZ (gauss^2)
- [22 ... 23] 风速NE (m/s)^2

### 观测创新与创新方差

观测`estimator_innovations`、`estimator_innovation_variances`和`estimator_innovation_test_ratios`消息字段在[EKF创新数据](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)中定义。所有消息具有相同的字段名称/类型（但单位不同）。

::: info
消息字段相同是因为它们来自相同的字段定义。`# TOPICS`行（在[文件末尾](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)）列出了要创建的消息集名称）：

```

# 主题 estimator_innovations estimator_innovation_variances estimator_innovation_test_ratios

```

:::

部分观测值包括：

- 磁力计 XYZ（高斯，高斯²）: `mag_field[3]`  
- 偏航角（弧度，弧度²）: `heading`  
- 真实空速（米/秒，（米/秒）²）: `airspeed`  
- 合成侧滑（弧度，弧度²）: `beta`  
- 光流 XY（弧度/秒，（弧度/秒）²）: `flow`  
- 离地高度（米，米²）: `hagl`  
- 阻力比力（（米/秒）²）: `drag`  
- 速度和位置创新值 : 按传感器划分  

此外，每个传感器都有其自身的水平和/或垂直位置和/或速度值的字段（如适用）。  
这些字段大多具有自描述性，如下所示：  

```
# GPS
float32[2] gps_hvel	# 水平GPS速度创新值 (m/sec) 和创新方差 ((m/sec)**2)
float32    gps_vvel	# 垂直GPS速度创新值 (m/sec) 和创新方差 ((m/sec)**2)
float32[2] gps_hpos	# 水平GPS位置创新值 (m) 和创新方差 (m**2)
float32    gps_vpos	# 垂直GPS位置创新值 (m) 和创新方差 (m**2)

# 外部视觉
float32[2] ev_hvel	# 水平外部视觉速度创新（米/秒）和创新方差（（米/秒）²）
float32    ev_vvel	# 垂直外部视觉速度创新（米/秒）和创新方差（（米/秒）²）
float32[2] ev_hpos	# 水平外部视觉位置创新（米）和创新方差（米²）
float32    ev_vpos	# 垂直外部视觉位置创新（米）和创新方差（米²）

# 虚拟位置和速度
float32[2] fake_hvel	# 虚拟水平速度创新 (m/s) 和创新方差 ((m/s)**2)
float32    fake_vvel	# 虚拟垂直速度创新 (m/s) 和创新方差 ((m/s)**2)
float32[2] fake_hpos	# 虚拟水平位置创新 (m) 和创新方差 (m**2)
float32    fake_vpos	# 虚拟垂直位置创新 (m) 和创新方差 (m**2)

# 高度传感器
float32 rng_vpos	# 测距传感器高度创新值 (m) 和创新值方差 (m**2)
float32 baro_vpos	# 气压计高度创新值 (m) 和创新值方差 (m**2)

# 辅助速度
float32[2] aux_hvel	# 水平辅助速度创新，来自着陆目标测量（m/sec）和创新方差 ((m/sec)**2)
float32    aux_vvel	# 垂直辅助速度创新，来自着陆目标测量（m/sec）和创新方差 ((m/sec)**2)

```

### 输出互补滤波器

输出互补滤波器用于从融合时间范围向当前时间传播状态。要检查融合时间范围内测量的角速度、速度和位置跟踪误差幅度，请参考`ekf2_innovations`消息中的`output_tracking_error[3]`。

索引映射如下：

- [0] 角度跟踪误差幅度（rad）
- [1] 速度跟踪误差幅度（m/s）。
  速度跟踪时间常数可通过[EKF2_TAU_VEL](../advanced_config/parameter_reference.md#EKF2_TAU_VEL)参数调整。
  减小此参数会减少稳态误差，但会增加NED速度输出的观测噪声。
- [2] 位置跟踪误差幅度（m）。
  位置跟踪时间常数可通过[EKF2_TAU_POS](../advanced_config/parameter_reference.md#EKF2_TAU_POS)参数调整。
  减小此参数会减少稳态误差，但会增加NED位置输出的观测噪声。

### EKF错误

EKF包含对状态和协方差更新中条件不佳的内部错误检查。请参阅[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中的`filter_fault_flags`。

### 观测错误

观测故障分为两类：

- 数据丢失。
  例如测距仪未能返回数据。
- 状态预测与传感器观测之间的创新过大。
  例如剧烈振动导致垂直位置误差过大，从而导致气压计高度测量被拒绝。

这两种情况可能导致观测数据被拒绝时间过长，从而触发EKF尝试使用传感器观测重置状态。所有观测都会对创新应用统计置信检查。检查的标准差数量由每种观测类型的`EKF2_*_GATE`参数控制。

[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中提供以下测试级别：

- `mag_test_ratio`：最大磁力计创新分量与创新测试限制的比值
- `vel_test_ratio`：最大速度创新分量与创新测试限制的比值
- `pos_test_ratio`：最大水平位置创新分量与创新测试限制的比值
- `hgt_test_ratio`：垂直位置创新与创新测试限制的比值
- `tas_test_ratio`：真实空速创新与创新测试限制的比值
- `hagl_test_ratio`：地面以上高度创新与创新测试限制的比值

对于每个传感器的二进制通过/失败摘要，请参阅[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中的`innovation_check_flags`。

### GPS质量检查

在开始GPS辅助之前，EKF会应用多项GPS质量检查。这些检查由[EKF2_GPS_CHECK](../advanced_config/parameter_reference.md#EKF2_GPS_CHECK)和`EKF2_REQ_*`参数控制。这些检查的通过/失败状态记录在[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).gps_check_fail_flags消息中。当所有必要GPS检查通过时，该整数为零。如果EKF未开始GPS对齐，请将整数值与[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)中的`gps_check_fail_flags`位掩码定义进行比对。

### EKF数值错误

EKF使用单精度浮点运算进行所有计算，并采用一阶近似推导协方差预测和更新方程，以减少处理需求。这意味着在重新调整EKF时，可能遇到协方差矩阵操作条件不佳的情况，导致发散或状态估计显著误差。

为防止这种情况，每个协方差和状态更新步骤包含以下错误检测和纠正步骤：

- 如果创新方差小于观测方差（这需要负的状态方差，这是不可能的）或协方差更新会产生任何状态的负方差，则：
  - 跳过状态和协方差更新
  - 重置协方差矩阵中的对应行和列
  - 在[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg) `filter_fault_flags`消息中记录失败
- 状态方差（协方差矩阵的对角线）被限制为非负数。
- 对状态方差应用上限。
- 强制协方差矩阵对称。

重新调整滤波器后，特别是涉及降低噪声变量的调整，应检查`estimator_status.gps_check_fail_flags`的值以确保其保持为零。

## 如果高度估计出现发散，我该怎么办？

EKF高度在飞行过程中与GPS和气压计测量值发生发散的最常见原因是由于振动导致的IMU测量值出现剪切和/或混叠现象。  
如果发生这种情况，数据中应出现以下特征：

- [EstimatorInnovations](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg).vel_pos_innov[2] 和 [EstimatorInnovations](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg).vel_pos_innov[5] 将具有相同的符号。  
- [EstimatorStatus](https.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).hgt_test_ratio 将大于1.0  

建议的首要步骤是确保飞控模块通过有效的隔离安装系统与机体分离。  
隔离安装系统具有6个自由度，因此有6个谐振频率。  
一般来说，飞控模块在隔离安装系统上的6个谐振频率应高于25Hz，以避免与飞控动态相互作用，并低于电机的频率。  

如果谐振频率与电机或螺旋桨叶片通过频率重合，隔离安装系统可能会使振动加剧。  

通过以下参数调整，可以增强EKF对振动引起的高度发散的抗性：  

- 将主要高度传感器的创新门值加倍。  
  如果使用气压高度，参数为 [EKF2_BARO_GATE](../advanced_config/parameter_reference.md#EKF2_BARO_GATE)。  
- 初始时将 [EKF2_ACC_NOISE](../advanced_config/parameter_reference.md#EKF2_ACC_NOISE) 的值提高到0.5。  
  如果发散仍然发生，可逐步增加0.1，但不要超过1.0  

请注意，这些调整会使EKF对GPS垂直速度误差和气压误差更加敏感。

## 如果位置估计发散，我应该怎么做？

位置发散的常见原因包括：

- 高振动水平。
  - 通过改进飞控的机械隔离来解决。
  - 增大[EKF2_ACC_NOISE](../advanced_config/parameter_reference.md#EKF2_ACC_NOISE)和[EKF2_GYR_NOISE](../advanced_config/parameter_reference.md#EKF2_GYR_NOISE)的值可能有所帮助，但会使EKF更容易受到GPS瞬时故障的影响。
- 大幅度陀螺偏置误差。
  - 通过重新校准陀螺来解决。
    检查温度敏感性是否过大（> 3 deg/sec在冷启动升温期间的偏置变化），若受影响则更换传感器或进行隔热处理以减缓温度变化率。
- 错误的偏航对齐。
  - 检查磁力计校准和对齐。
  - 检查QGC显示的航向是否在15度以内。
- 较差的GPS精度。
  - 检查是否存在干扰。
  - 改善分离度和屏蔽。
  - 检查飞行位置的GPS信号遮挡和反射（附近的高楼）。
- GPS信号丢失。

确定这些原因中的主要因素需要系统分析EKF日志数据的方法：

- 绘制速度创新测试比率 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vel_test_ratio
- 绘制水平位置创新测试比率 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).pos_test_ratio
- 绘制高度创新测试比率 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).hgt_test_ratio
- 绘制磁力计创新测试比率 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).mag_test_ratio
- 绘制GPS接收机报告的速度精度 - [SensorGps.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGps.msg).s_variance_m_s
- 绘制IMU角变化状态估计 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).states[10], states[11] 和 states[12]
- 绘制EKF内部高频振动指标：
  - 角锥振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[0]
  - 高频角变化振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[1]
  - 高频速度变化振动 - [EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vibe[2]

在正常运行期间，所有测试比率应保持在0.5以下，偶尔会有短暂的峰值，如下图所示的成功飞行示例：

![Position, Velocity, Height and Magnetometer Test Ratios](../../assets/ecl/test_ratios_-_successful.png)

下图显示了具有良好隔离的多旋翼的EKF振动指标。
可以观察到着陆冲击以及起降时的振动增加。
目前通过这些指标收集的数据不足以提供具体的最大阈值建议。

![Vibration metrics - successful](../../assets/ecl/vibration_metrics_-_successful.png)

上述振动指标的参考价值有限，因为接近IMU采样频率（大多数板卡为1 kHz）的振动会导致数据中出现偏移，这些偏移在高频振动指标中不会显示。
检测混叠误差的唯一方法是通过其对惯性导航精度和创新水平上升的影响。

除了产生大于1.0的大位置和速度测试比率外，不同的错误机制会影响其他测试比率的方式也不同：

### 过度振动的判定

高振动水平通常会影响垂直和水平位置及速度的创新，磁力计测试水平仅受轻微影响。

（在此插入显示不良振动的示例图）

### 过度陀螺偏置的判定

大陀螺偏置误差通常表现为飞行过程中角变化偏置值的变化超过5E-4（相当于~3 deg/sec），如果影响到偏航轴，还会导致磁力计测试比率大幅增加。
除极端情况外，高度通常不受影响。
最多5 deg/sec的偏置值在滤波器有足够时间稳定的情况下是可以容忍的。
指挥官执行的预飞检查应阻止在位置发散时解锁。

（在此插入显示不良陀螺偏置的示例图）

### 偏航精度差的判定

不良偏航对齐会导致机体开始移动时速度测试比率迅速增加，因为惯性导航计算的速度方向与GPS测量不一致。
磁力计创新略有影响。
高度通常不受影响。

（在此插入显示不良偏航对齐的示例图）

### GPS精度差的判定

GPS精度差通常伴随着接收机报告的速度误差上升以及创新值的增加。
多路径、遮挡和干扰引起的瞬时错误更为常见。
以下是一个GPS精度暂时丢失的示例，其中多旋翼开始偏离悬停位置，需要使用操纵杆进行修正。
[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vel_test_ratio上升到1以上表明GPS速度与其他测量不一致并已被拒绝。

![GPS glitch - test ratios](../../assets/ecl/gps_glitch_-_test_ratios.png)

这伴随着GPS接收机报告的速度精度上升，表明存在干扰。

（在此插入显示GPS精度的示例图）

### GPS信号丢失的判定

GPS信号丢失通常表现为接收机报告的速度误差上升以及创新值的增加。
多路径、遮挡和干扰引起的瞬时错误更为常见。
以下是一个GPS精度暂时丢失的示例，其中多旋翼开始偏离悬停位置，需要使用操纵杆进行修正。
[EstimatorStatus](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg).vel_test_ratio上升到1以上表明GPS速度与其他测量不一致并已被拒绝。

（在此插入显示GPS信号丢失的示例图）

### 地面效应补偿的设置

如果可用地形估计（例如，机体配备测距仪），则可以另外指定[EKF2_GND_MAX_HGT](../advanced_config/parameter_reference.md#EKF2_GND_MAX_HGT)，即地面效应补偿应激活的高于地面的海拔值。
如果无法获得地形估计，此参数将无效，系统将使用启发式方法确定是否激活地面效应补偿。

## 更多信息

- [PX4 State Estimation Overview](https://youtu.be/HkYRJJoyBwQ), _PX4 Developer Summit 2019_, Dr. Paul Riseborough): 估计器概述，2018/19年的重大更改以及2019/20年预期的改进。