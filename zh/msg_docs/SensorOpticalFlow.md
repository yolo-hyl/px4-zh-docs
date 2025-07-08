# 光流传感器 (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorOpticalFlow.msg)  

```c  
uint64 timestamp               # 系统启动后的时间 (微秒)  
uint64 timestamp_sample  

uint32 device_id               # 传感器的唯一设备ID，断电重启后保持不变  

float32[2] pixel_flow          # (弧度) 像素流以弧度表示，正值由绕机体轴的右手法则旋转产生  

float32[3] delta_angle         # (弧度) 累计的陀螺仪弧度，正值由绕机体轴的右手法则旋转产生。如果光流传感器没有三轴陀螺仪数据则设为NaN  
bool delta_angle_available  

float32 distance_m             # (米) 距光流场中心的距离  
bool distance_available  

uint32 integration_timespan_us # (微秒) 累计时间跨度，单位为微秒  

uint8 quality                  # 质量，0: 质量差，255: 最大质量  

uint32 error_count  

float32 max_flow_rate          # (弧度/秒) 光流传感器能可靠测量的最大角速度  

float32 min_ground_distance    # (米) 光流传感器能可靠工作的最低地面距离  
float32 max_ground_distance    # (米) 光流传感器能可靠工作的最高地面距离  

uint8 MODE_UNKNOWN        = 0  
uint8 MODE_BRIGHT         = 1  
uint8 MODE_LOWLIGHT       = 2  
uint8 MODE_SUPER_LOWLIGHT = 3  

uint8 mode  
```