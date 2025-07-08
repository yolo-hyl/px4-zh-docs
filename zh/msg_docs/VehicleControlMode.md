# 机体控制模式 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleControlMode.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp		# 系统启动后时间戳（微秒）
bool flag_armed			# actuator_armed.armed 的同义词

bool flag_multicopter_position_control_enabled

bool flag_control_manual_enabled		# 如果包含手动输入则为true
bool flag_control_auto_enabled			# 如果应启用机载自动驾驶则为true
bool flag_control_offboard_enabled		# 如果应使用外部控制则为true
bool flag_control_position_enabled		# 如果位置控制启用则为true
bool flag_control_velocity_enabled		# 如果水平速度（含方向）控制启用则为true
bool flag_control_altitude_enabled		# 如果高度控制启用则为true
bool flag_control_climb_rate_enabled		# 如果爬升率控制启用则为true
bool flag_control_acceleration_enabled		# 如果加速度控制启用则为true
bool flag_control_attitude_enabled		# 如果姿态稳定控制启用则为true
bool flag_control_rates_enabled			# 如果角速度稳定控制启用则为true
bool flag_control_allocation_enabled		# 如果控制分配启用则为true
bool flag_control_termination_enabled		# 如果飞行终止控制启用则为true

# TODO: 使用专用话题处理外部请求
uint8 source_id                  # 模式ID (导航状态)

# TOPICS vehicle_control_mode config_control_setpoints

```