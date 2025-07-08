# 空速 (UORB message)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Airspeed.msg)  

```c  
uint64 timestamp                 # 系统启动时间（微秒）  
uint64 timestamp_sample  

float32 indicated_airspeed_m_s   # 表速（米/秒）  

float32 true_airspeed_m_s        # 真空速（米/秒）  

float32 confidence               # 传感器置信度（范围 0 到 1）  
```