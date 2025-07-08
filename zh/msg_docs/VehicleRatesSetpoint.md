# 机体角速率设定值 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleRatesSetpoint.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp	# 系统启动后经过的时间（微秒）

# 机体在FRD坐标系下的角速率
float32 roll		# [rad/s] 滚转角速率设定值
float32 pitch		# [rad/s] 俯仰角速率设定值
float32 yaw		# [rad/s] 偏航角速率设定值

# 说明：对于多旋翼，thrust_body[0]和thrust[1]通常为0，thrust[2]为负的油门需求。
# 对于固定翼，thrust_x为油门需求，thrust_y和thrust_z通常为零。
float32[3] thrust_body	# 机体NED坐标系下的归一化推力指令 [-1,1]

bool reset_integral # 重置滚转/俯仰/偏航积分（导航逻辑变更）
```