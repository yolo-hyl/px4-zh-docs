# 传感器陀螺（UORB message）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGyro.msg)  

```c  
uint64 timestamp          # 系统启动后经过的时间（微秒）  
uint64 timestamp_sample  

uint32 device_id          # 传感器的唯一设备ID（断电后不变）  

float32 x                 # FRD板坐标系X轴的角速度（rad/s）  
float32 y                 # FRD板坐标系Y轴的角速度（rad/s）  
float32 z                 # FRD板坐标系Z轴的角速度（rad/s）  

float32 temperature       # 温度（摄氏度）  

uint32 error_count  

uint8[3] clip_counter     # 采样周期内各轴的裁剪计数  

uint8 samples             # 生成本消息所使用的原始样本数量  

uint8 ORB_QUEUE_LENGTH = 8  
```