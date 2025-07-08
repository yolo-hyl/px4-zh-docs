# 陀螺仪硬件与设置

PX4 使用陀螺仪来估计机体姿态（方位）。

通常无需单独附加陀螺仪设备：

- 大多数飞控（如 [Pixhawk Series](../flight_controller/pixhawk_series.md)）均内置陀螺仪，作为飞控 [惯性运动单元（IMU）](https://en.wikipedia.org/wiki/Inertial_measurement_unit) 的组成部分。
- 外部 [惯性导航系统（INS）、姿态参考系统（ARHS）或增强型GNSS系统](../sensor/inertial_navigation_systems.md) 也会集成陀螺仪。

在首次使用机体前必须对陀螺仪进行校准：

- [陀螺仪校准](../config/gyroscope.md)