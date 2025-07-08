# 传感器陀螺仪FIFO (UORB消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGyroFifo.msg)

```c
uint64 timestamp          # 自系统启动以来的时间（微秒）
uint64 timestamp_sample

uint32 device_id          # 传感器的唯一设备ID，断电重启后保持不变

float32 dt                # 样本间隔时间（微秒）
float32 scale

uint8 samples             # 有效样本数量

int16[32] x               # FRD板坐标系X轴的角速度（弧度/秒）
int16[32] y               # FRD板坐标系Y轴的角速度（弧度/秒）
int16[32] z               # FRD板坐标系Z轴的角速度（弧度/秒）

uint8 ORB_QUEUE_LENGTH = 4

```