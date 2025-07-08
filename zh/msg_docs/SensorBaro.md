# SensorBaro (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorBaro.msg)

```c
uint64 timestamp          # 系统启动后经过的时间（微秒）
uint64 timestamp_sample

uint32 device_id          # 唯一设备ID（断电重启后保持不变）

float32 pressure          # 静压测量值（帕斯卡）

float32 temperature       # 温度（摄氏度）

uint32 error_count

uint8 ORB_QUEUE_LENGTH = 4

```