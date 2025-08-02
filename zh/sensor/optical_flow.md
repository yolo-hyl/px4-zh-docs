# 光学流

_Optical Flow_ 使用向下视角的摄像头和向下视角的测距传感器进行速度估计。  
可在无GNSS环境下用于导航速度确定，例如在建筑物内、地下或任何GNSS信号缺失的环境中。

下方视频展示了PX4在[Position Mode](../flight_modes_mc/position.md)中使用[Ark Flow](../dronecan/ark_flow.md)传感器进行速度估计并保持位置：

<lite-youtube videoid="aPQKgUof3Pc" title="ARK Flow with PX4 Optical Flow Position Hold"/>

<!-- ARK Flow with PX4 Optical Flow Position Hold: 20210605 -->

下方图片展示了使用独立光学流传感器([PX4Flow](../sensor/px4flow.md))和测距传感器([Lidar-Lite](../sensor/lidar_lite.md))的光学流配置示例：

![Optical flow lidar attached](../../assets/hardware/sensors/optical_flow/flow_lidar_attached.jpg)

## 设置

光学流配置需要一个向下视角的摄像头和一个向下视角的[测距传感器](../sensor/rangefinders.md)（建议使用LiDAR）。  
这些设备可以集成在单个产品中，如[ARK Flow](../dronecan/ark_flow.md)、[ARK Flow MR](../dronecan/ark_flow_mr.md)和[Holybro H-Flow](https://holybro.com/products/h-flow)，也可以作为独立传感器使用。

传感器可通过MAVLink、I2C或任何支持外围设备的总线连接。

::: info
若通过MAVLink连接到PX4，光学流摄像头传感器必须发布[OPTICAL_FLOW_RAD](https://mavlink.io/en/messages/common.html#OPTICAL_FLOW_RAD)消息，测距传感器必须发布[DISTANCE_SENSOR](https://mavlink.io/en/messages/common.html#DISTANCE_SENSOR)消息。  
数据将写入对应的uORB话题：[DistanceSensor](../msg_docs/DistanceSensor.md)和[ObstacleDistance](../msg_docs/ObstacleDistance.md)。
:::

设备在不同方向移动时的输出应符合下表：

| 机体移动 | 集成光流 |
| -------- | -------- |
| 向前     | + Y      |
| 向后     | - Y      |
| 右       | - X      |
| 左       | + X      |

光学流设备的传感器数据会与其他速度数据源融合。  
用于融合传感器数据的方法及相对于机体中心的偏移量必须在[estimator](#估计器)中配置。

### 缩放因子

在纯旋转情况下，`OPTICAL_FLOW_RAD.integrated_xgyro`和`OPTICAL_FLOW_RAD.integrated_x`（及`integrated_ygyro`和`integrated_y`）的值必须相同。  
若此条件不满足，可通过[SENS_FLOW_SCALE](../advanced_config/parameter_reference.md#SENS_FLOW_SCALE)调整光学流缩放因子。

:::tip
常见光学流传感器的低分辨率可能导致在高空悬停（>20米）时出现缓慢振荡。  
降低光学流缩放因子可改善此情况。
:::

## 光学流传感器/摄像头

### ARK Flow & ARK Flow MR

[ARK Flow](../dronecan/ark_flow.md) 是一个[DroneCAN](../dronecan/index.md)光学流传感器、[测距传感器](../sensor/rangefinders.md)和IMU组合。  
包含PAW3902光学流传感器、Broadcom AFBR-S50LV85D 30米测距传感器和Invensense ICM-42688-P六轴IMU。

[ARK Flow MR](../dronecan/ark_flow_mr.md) 是适用于中程应用的[DroneCAN](../dronecan/index.md)光学流传感器、[测距传感器](../sensor/rangefinders.md)和IMU组合。  
包含PixArt PAA3905光学流传感器、Broadcom AFBR-S50LX85D 50米测距传感器和Invensense IIM-42653六轴IMU。

### Holybro H-Flow

[Holybro H-Flow](https://holybro.com/products/h-flow) 是一个紧凑型[DroneCAN](../dronecan/index.md)光学流和[测距传感器](../sensor/rangefinders.md)模块。  
集成了PixArt PAA3905光学流传感器、Broadcom AFBR-S50LV85D测距传感器和InvenSense ICM-42688-P六轴IMU。  
一体化设计简化安装，内置红外LED增强低光条件下的可见性。

### PMW3901-Based Sensors

[PMW3901](../sensor/pmw3901.md) 是一种光学流追踪传感器，其工作原理类似于计算机鼠标，但调整为在80 mm至无限远范围内工作。  
被Bitcraze、Tindie、Hex、Thone和Alientek等厂商的多种产品采用。

### 其他摄像头/传感器

也可以使用集成摄像头的板载设备。  
可使用[Optical Flow repo](https://github.com/PX4/OpticalFlow)（另见[snap_cam](https://github.com/PX4/snap_cam)）实现。

## 测距传感器

可使用任何支持的[测距传感器](../sensor/rangefinders.md)。  
建议使用LIDAR而非超声波传感器，因其更可靠且精度更高。

## 估计器

估计器会融合光学流传感器及其他数据源。  
必须为所使用的估计器指定融合方式及相对于机体中心的偏移量。

偏移量根据机体方向和中心计算，如下图所示：

![Optical Flow offsets](../../assets/hardware/sensors/optical_flow/px4flow_offset.png)

基于光学流的导航由[EKF2](#ekf2)和LPE（已弃用）共同启用。

### 扩展卡尔曼滤波器（EKF2）{#ekf2}

使用EKF2进行光学流融合时，需设置[EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL)。

若光学流传感器与机体中心存在偏移，可通过以下参数设置：

| 参数                                                                                          | 描述                                                             |
| -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| <a id="EKF2_OF_POS_X"></a>[EKF2_OF_POS_X](../advanced_config/parameter_reference.md#EKF2_OF_POS_X) | 机体坐标系下光学流焦点的X位置（默认0.0）                             |
| <a id="EKF2_OF_POS_Y"></a>[EKF2_OF_POS_Y](../advanced_config/parameter_reference.md#EKF2_OF_POS_Y) | 机体坐标系下光学流焦点的Y位置（默认0.0）                             |
| <a id="EKF2_OF_POS_Z"></a>[EKF2_OF_POS_Z](../advanced_config/parameter_reference.md#EKF2_OF_POS_Z) | 机体坐标系下光学流焦点的Z位置（默认0.0）                             |

更多详情请参考[Optical Flow Position Estimation](../advanced/optical_flow_position_estimation.md)。