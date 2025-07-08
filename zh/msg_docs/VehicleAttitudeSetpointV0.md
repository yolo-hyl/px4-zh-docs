# 机体姿态设定点V0 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/px4_msgs_old/msg/VehicleAttitudeSetpointV0.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp		# 系统启动后的时间（微秒）

float32 yaw_sp_move_rate	# 用户指令的角速度（弧度/秒）

# 用于四元数姿态控制
float32[4] q_d			# 四元数控制的期望四元数

# 说明：对于多旋翼，thrust_body[0]和thrust[1]通常为0，thrust[2]是负的油门需求。
# 对于固定翼，thrust_x是油门需求，thrust_y和thrust_z通常为0。
float32[3] thrust_body		# 机体FRD坐标系中的归一化推力指令[-1,1]

bool reset_integral	# 重置滚转/俯仰/偏航积分（导航逻辑变更）

bool fw_control_yaw_wheel	# 使用转向盘控制航向（用于跑道自动起飞）

# 主题 vehicle_attitude_setpoint mc_virtual_attitude_setpoint fw_virtual_attitude_setpoint
```