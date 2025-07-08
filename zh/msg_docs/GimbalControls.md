# 云台控制器（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GimbalControls.msg)

```c
uint64 timestamp			# 系统启动以来的时间（微秒）
uint8 INDEX_ROLL = 0
uint8 INDEX_PITCH = 1
uint8 INDEX_YAW = 2

uint64 timestamp_sample	    # 数据采样时间戳（该控制响应基于此数据）
float32[3] control

```