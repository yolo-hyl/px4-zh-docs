# 机体局部位置设定点 (UORB message)

NED坐标系下的局部位置设定点
PID位置控制器的遥测数据用于监控跟踪。
NaN表示该状态未被控制

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleLocalPositionSetpoint.msg)

```c
# 局部位置设定点在NED坐标系中
# PID位置控制器的遥测数据用于监控跟踪
# NaN表示该状态未被控制

uint64 timestamp	# 自系统启动以来的时间（微秒）

float32 x		# 以米为单位，NED坐标系
float32 y		# 以米为单位，NED坐标系
float32 z		# 以米为单位，NED坐标系

float32 vx		# 以米/秒为单位
float32 vy		# 以米/秒为单位
float32 vz		# 以米/秒为单位

float32[3] acceleration # 以米/秒²为单位
float32[3] thrust	# 归一化的推力向量，NED坐标系

float32 yaw		# 以弧度为单位，NED坐标系 -PI..+PI
float32 yawspeed	# 以弧度/秒为单位
```