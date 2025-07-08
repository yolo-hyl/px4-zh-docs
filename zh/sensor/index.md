# 传感器硬件与设置

本节描述了必需和可选传感器及其设置/配置。

## 概述

基于PX4的系统使用传感器来估计机体状态（用于稳定性和自主控制）。
机体状态信息包括：位置/高度、航向、速度、空速、姿态（方向）、不同方向的旋转速率、电池电量等。

PX4 _至少需要_ 陀螺仪、加速度计、磁力计（指南针）和气压计来测量上述状态。
固定翼和VTOL机体 _建议_ 配备空速传感器。
需要GPS或其他定位系统来启用所有自动模式，以及部分手动/辅助模式。

[Pixhawk系列](../flight_controller/pixhawk_series.md)飞控已经包含基础传感器（其他控制器平台通常也是如此）。
可附加外部传感器到控制器——建议使用外部GPS和指南针，以及VTOL和固定翼机体的空速传感器。

## 传感器主题

必选（Pixhawk系列飞控已包含）：

- [加速度计](../sensor/accelerometer.md) — 测量加速度变化。
- [陀螺仪](../sensor/gyroscope.md) — 测量姿态。
- [磁力计（指南针）](../gps_compass/magnetometer.md) — 测量航向/方向。
  推荐使用外部指南针！
- [气压计](../sensor/barometer.md) — 通过气压测量高度。

推荐：

- [空速传感器](../sensor/airspeed.md) — 测量空速。
  对VTOL和固定翼机体高度推荐，因为这是检测失速的唯一机制。
- [GNSS（GPS）](../gps_compass/index.md) — 测量全局位置。
  任务、部分自动模式及手动/辅助模式需要。
- [RTK GNSS（GPS）](../gps_compass/rtk_gps.md) — 厘米级精度的GNSS。
  某些配置还可通过GNSS而非磁力计确定航向。

可选：

- [距离传感器（测距仪）](../sensor/rangefinders.md) — 测量到目标的距离。
  有助于着陆、避障和地形跟随。
- [光流传感器](../sensor/optical_flow.md) — 使用向下摄像头和向下测距仪估算速度。
  可提供比GPS更精确的位置锁定，并可在无GPS信号时用于室内定位。
- [转速计（转数计）](../sensor/tachometers.md) — 仅用于日志记录。

其他可选：

- [IMU/指南针出厂校准](../advanced_config/imu_factory_calibration.md) — 将校准设置保存到持久化存储。
- [传感器热补偿](../advanced_config/sensor_thermal_calibration.md) — 对温度变化进行传感器补偿。