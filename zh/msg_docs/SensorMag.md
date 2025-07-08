# SensorMag (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorMag.msg)

```c
uint64 timestamp          # 自系统启动以来的时间（微秒）
uint64 timestamp_sample

uint32 device_id          # 唯一设备ID，传感器在断电重启后也不会改变

float32 x                 # FRD板坐标系X轴的磁场强度（高斯）
float32 y                 # FRD板坐标系Y轴的磁场强度（高斯）
float32 z                 # FRD板坐标系Z轴的磁场强度（高斯）

float32 temperature       # 温度（摄氏度）

uint32 error_count

uint8 ORB_QUEUE_LENGTH = 4

```