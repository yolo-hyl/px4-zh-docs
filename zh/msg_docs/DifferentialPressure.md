# 差分压力传感器 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DifferentialPressure.msg)

```c
uint64 timestamp                     # 系统启动后的时间戳（微秒）
uint64 timestamp_sample

uint32 device_id                     # 传感器的唯一设备ID（断电后不变）

float32 differential_pressure_pa     # 差分压力读数，单位为帕斯卡（可能为负值）

float32 temperature                  # 传感器提供的温度，单位为摄氏度，未知时为NAN

uint32 error_count                   # 驱动检测到的错误数量

```