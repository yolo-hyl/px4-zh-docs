# 计算机视觉（光流、运动捕捉、VIO、避障）

[计算机视觉](https://en.wikipedia.org/wiki/Computer_vision) 技术使计算机能够通过视觉数据理解周围环境。

PX4 通过运行在 [Companion Computers](../companion_computer/index.md) 上的计算机视觉系统支持以下功能：

- [光流](#光流) 提供2D速度估计（使用向下摄像头和向下距离传感器）。
- [运动捕捉](#运动捕捉) 通过外部视觉系统提供3D位姿估计，主要应用于室内导航。
- [视觉惯性里程计](#visual-inertial-odometry-vio) (VIO) 通过机载视觉系统和IMU提供3D位姿和速度估计，用于GNSS定位信息缺失或不可靠时的导航。
- [碰撞预防](../computer_vision/collision_prevention.md) 用于在飞行器即将碰撞障碍物前主动停止（主要在手动模式飞行时）。

:::tip
[PX4 Vision Autonomy Development Kit](../complete_vehicles_mc/px4_vision_kit.md)（Holybro）是针对PX4计算机视觉开发的可靠且经济的开发套件。
:::

## 运动捕捉

运动捕捉（MoCap）是通过外部定位机制估计机体3D位姿（位置和姿态）的技术。MoCap系统通常使用红外摄像机检测运动，但也可以使用其他类型摄像机、激光雷达或超宽带（UWB）。

::: info
MoCap常用于GPS缺失场景（例如室内导航），提供相对于本地坐标系的位置信息。
:::

关于MoCap的信息请参考：

- [External Position Estimation](../ros/external_position_estimation.md)
- [Flying with Motion Capture (VICON, NOKOV, Optitrack)](../tutorials/motion-capture.md)
- [Using PX4's Navigation Filter (EKF2) > External Vision System](../advanced_config/tuning_the_ecl_ekf.md#external-vision-system)

## 视觉惯性里程计（VIO）

视觉惯性里程计（VIO）用于估计机体相对于本地起始位置的3D位姿（位置和姿态）及速度。当GPS缺失（例如室内场景）或不可靠（例如桥梁下方飞行）时，VIO常用于机体导航。

VIO通过[视觉里程计](https://en.wikipedia.org/wiki/Visual_odometry)从视觉信息中估计位姿，结合IMU惯性测量数据（用于修正快速运动导致的图像采集误差）。

::: info
VIO与[MoCap](#运动捕捉)的主要区别在于：VIO的摄像头/IMU安装在机体上，并且提供速度信息。
:::

关于在PX4上配置VIO的信息请参考：

- [Using PX4's Navigation Filter (EKF2) > External Vision System](../advanced_config/tuning_the_ecl_ekf.md#external-vision-system)
- [T265 Setup guide](../peripherals/camera_t265_vio.md)

## 光流

[光流](../sensor/optical_flow.md) 提供2D速度估计（使用向下摄像头和向下距离传感器）。

关于光流的更多信息请参考：

- [Optical Flow](../sensor/optical_flow.md)
- [Using PX4's Navigation Filter (EKF2) > Optical Flow](../advanced_config/tuning_the_ecl_ekf.md#optical-flow)

## 对比分析

### 光流与VIO在局部位置估计中的对比

这两种技术均使用摄像头并测量帧间差异。光流使用向下摄像头，而VIO使用立体摄像头或45度追踪摄像头。假设两者都经过良好校准，哪种更适合局部位置估计？

[讨论结论](https://discuss.px4.io/t/vio-vs-optical-flow/34680)显示：

**光流：**
- 向下光流通过陀螺仪修正角速度，提供平面速度估计
- 需要精确的地面距离测量并假设平面表面。在满足这些条件时，其准确度和可靠性可与VIO相当（如室内飞行）
- 状态较少，鲁棒性优于VIO
- 仅需流传感器、测距仪和少量参数设置，显著更便宜且易于部署（可连接飞控器）

**VIO：**
- 购买成本更高且部署更复杂，需要独立的伴飞计算机、校准、软件配置等
- 若环境中缺乏可追踪特征点（实践中现实世界通常存在特征点）则效果下降
- 更具灵活性，支持避障和地图构建等附加功能

融合使用两者的方案可能最可靠，但多数实际场景并不需要。通常根据操作环境、功能需求和成本约束选择系统：

- 若计划在无GPS环境下（或室内外混合环境）飞行，或需要支持避障等计算机视觉功能，选择VIO
- 若仅在室内（无GPS）飞行且成本是重要因素，选择光流

## 外部资源

- [XTDrone](https://github.com/robin-shaun/XTDrone/blob/master/README.en.md) - 计算机视觉的ROS+PX4仿真环境。[XTDrone Manual](https://www.yuque.com/xtdrone/manual_en) 包含所有入门所需内容！