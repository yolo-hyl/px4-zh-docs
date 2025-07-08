# SensorAccelFifo (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorAccelFifo.msg)

```c
uint64 timestamp          # 系统启动后的时间 (微秒)
uint64 timestamp_sample

uint32 device_id          # 传感器的唯一设备ID（断电重启后不变）

float32 dt                # 样本之间的时间间隔 (微秒)
float32 scale

uint8 samples             # 有效样本数量

int16[32] x               # 加速度在FRD板坐标系X轴上的值，单位为m/s^2
int16[32] y               # 加速度在FRD板坐标系Y轴上的值，单位为m/s^2
int16[32] z               # 加速度在FRD板坐标系Z轴上的值，单位为m/s^2

```