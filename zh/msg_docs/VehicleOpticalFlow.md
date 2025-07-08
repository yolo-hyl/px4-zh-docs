# 机体光流 (UORB消息)

机体坐标系中以国际单位制表示的光流数据。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleOpticalFlow.msg)

```c
# 机体坐标系中以国际单位制表示的光流数据。

uint64 timestamp               # 系统启动后的时间（微秒）
uint64 timestamp_sample

uint32 device_id               # 传感器的唯一设备ID，断电重启后保持不变

float32[2] pixel_flow          # (弧度) 累计光流弧度值，正值表示围绕机体轴的右手法则旋转

float32[3] delta_angle         # (弧度) 累计陀螺仪弧度值，正值表示传感器围绕机体轴的右手法则旋转 (不可用时为NAN)

float32 distance_m             # (米) 光流场中心距离 (不可用时为NAN)

uint32 integration_timespan_us # (微秒) 累计时间跨度

uint8 quality                  # 累计帧质量的平均值，0: 差质量，255: 最大质量

float32 max_flow_rate          # (弧度/秒) 光流传感器可可靠测量的最大角速度

float32 min_ground_distance    # (米) 光流传感器可靠工作的最小离地高度
float32 max_ground_distance    # (米) 光流传感器可靠工作的最大离地高度

```