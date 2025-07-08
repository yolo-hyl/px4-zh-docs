# 机体加速度（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAcceleration.msg)

```c

uint64 timestamp		# 系统启动后的时间（微秒）

uint64 timestamp_sample		# 原始数据的时间戳（微秒）

float32[3] xyz			# 偏置校正后的加速度（含重力）在FRD机体坐标系XYZ轴上的分量（米每二次方秒）

```