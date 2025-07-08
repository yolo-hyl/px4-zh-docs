# 机体磁力计 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleMagnetometer.msg)

```c

uint64 timestamp            # 系统启动以来的时间（微秒）

uint64 timestamp_sample     # 原始数据的时间戳（微秒）

uint32 device_id            # 所选磁力计的唯一设备ID

float32[3] magnetometer_ga  # FRD机体坐标系下XYZ轴的磁场（高斯）

uint8 calibration_count     # 校准变更计数器。每次校准更改时单调递增。

```