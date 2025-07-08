# 加速度计硬件与设置

PX4 使用加速度计数据进行速度估算。

通常无需将加速度计作为独立的外部设备安装：

- 大多数飞控（如 [Pixhawk 系列](../flight_controller/pixhawk_series.md)）已内置加速度计，作为飞控 [惯性运动单元 (IMU)](https://en.wikipedia.org/wiki/Inertial_measurement_unit) 的组成部分。
- 陀螺仪作为 [外部 INS、ARHS 或增强型 GNSS 系统](../sensor/inertial_navigation_systems.md) 的一部分存在。

在机体首次使用前必须对加速度计进行校准：

- [加速度计校准](../config/accelerometer.md)