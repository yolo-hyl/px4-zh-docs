# 计算机视觉（光流、运动捕捉、VIO、防撞）

[计算机视觉](https://en.wikipedia.org/wiki/Computer_vision) 技术使计算机能够通过视觉数据来理解其环境。

PX4 通过计算机视觉系统（主要运行在 [伴飞计算机](../companion_computer/index.md) 上）支持以下功能：

- 姿态/速度估计：
  - [光流](../sensor/optical_flow.md) 通过向下摄像头和向下距离传感器提供2D速度估计。
  - [运动捕捉](../computer_vision/motion_capture.md) 通过外部视觉系统提供3D姿态估计。
    主要用于室内导航。
  - [视觉惯性里程计（VIO）](../computer_vision/visual_inertial_odometry.md) 通过机载视觉系统和IMU提供3D姿态和速度估计。
    用于全局位置信息缺失或不可靠时的导航。
- 防撞/路径规划：
  - [防撞](../computer_vision/collision_prevention.md) 用于在多旋翼飞机撞上障碍物前进行阻止（主要在手动模式飞行时使用）。

:::tip
[PX4 Vision Autonomy Development Kit](../complete_vehicles_mc/px4_vision_kit.md)（Holybro）是开发者在PX4上使用计算机视觉的坚固且经济的套件。
:::

## 外部资源

- [XTDrone](https://github.com/robin-shaun/XTDrone/blob/master/README.en.md) - 面向计算机视觉的ROS + PX4 v1.9仿真环境。
  [XTDrone手册](https://www.yuque.com/xtdrone/manual_en) 包含了您开始所需的所有内容！