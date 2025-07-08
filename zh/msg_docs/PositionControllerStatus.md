# PositionControllerStatus (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PositionControllerStatus.msg)

```c
uint64 timestamp		# 系统启动以来的时间（微秒）

float32 nav_roll		# 滚转角度设定值 [rad]
float32 nav_pitch		# 俯仰角度设定值 [rad]
float32 nav_bearing 		# 航向角 [rad]
float32 target_bearing		# 飞机到当前目标的航向角 [rad]
float32 xtrack_error		# 航迹误差（带符号） [m]
float32 wp_dist			# 到当前（下一个）航点的距离 [m]
float32 acceptance_radius	# 当前水平接受半径 [m]
uint8 type			# 当前（已应用）位置设定值类型（参见PositionSetpoint.msg）

```