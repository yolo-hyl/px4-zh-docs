# GpsInjectData（UORB 消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GpsInjectData.msg)  

```c  
uint64 timestamp		# 系统启动后的时间（微秒）  

uint32 device_id                # 唯一设备ID（断电重启后不变）  

uint16 len                       # 数据长度  
uint8 flags                     # LSB: 1=分片  
uint8[300] data                 # 写入GPS设备的数据（RTCM消息）  

uint8 ORB_QUEUE_LENGTH = 8  

uint8 MAX_INSTANCES = 2  
```