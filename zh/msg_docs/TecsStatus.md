# TecsStatus（UORB消息）


[源文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TecsStatus.msg)

```c
uint64 timestamp		# 系统启动后经过时间（微秒）

float32 altitude_sp			# 高度设定值 AMSL [米]
float32 altitude_reference		# 高度参考值 AMSL [米]
float32 altitude_time_constant		# 高度跟踪器时间常数 [秒]
float32 height_rate_reference		# 高度率参考设定值 [米/秒]
float32 height_rate_direct		# 由速度参考生成器直接输出的高度率设定值 [米/秒]
float32 height_rate_setpoint		# 高度率设定值 [米/秒]
float32 height_rate			# 实际高度率 [米/秒]
float32 equivalent_airspeed_sp		# 等效空速设定值 [米/秒]
float32 true_andspeed_sp		# 真实空速设定值 [米/秒]
float32 true_airspeed_filtered		# 滤波后的真实空速 [米/秒]
float32 true_airspeed_derivative_sp	# 真实空速导数设定值 [米²/秒³]
float32 true_airspeed_derivative	# 真实空速导数 [米²/秒³]
float32 true_airspeed_derivative_raw	# 原始真实空速导数 [米²/秒³]

float32 total_energy_rate_sp		# 总能量变化率设定值 [米²/秒³]
float32 total_energy_rate		# 总能量变化率估计值 [米²/秒³]

float32 total_energy_balance_rate_sp	# 能量平衡变化率设定值 [米²/秒³]
float32 total_energy_balance_rate	# 能量平衡变化率估计值 [米²/秒³]

float32 throttle_integ			# 油门积分值 [-]
float32 pitch_integ			# 俯仰角积分值 [弧度]

float32 throttle_sp			# 当前油门设定值 [-]
float32 pitch_sp_rad			# 当前俯仰角设定值 [弧度]
float32 throttle_trim			# 在当前大气条件下，为维持等效空速设定值飞行所需的油门估计值 [0,1]

float32 underspeed_ratio		# 0: 无欠速, 1: 最大欠速。控制器会根据比例值采取措施防止失速（大于0时生效）。
float32 fast_descend_ratio 		# 表示快速下降模式是否启用的值（包含加速和减速阶段），范围 [0-1]

```