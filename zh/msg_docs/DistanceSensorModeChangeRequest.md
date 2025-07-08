# 距离传感器模式更改请求 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DistanceSensorModeChangeRequest.msg)

```c
uint64 timestamp		# 自系统启动以来的时间（微秒）

uint8 request_on_off 			# 请求启用/禁用距离传感器
uint8 REQUEST_OFF = 0
uint8 REQUEST_ON  = 1

```