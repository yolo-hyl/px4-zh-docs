# 空速验证（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/AirspeedValidated.msg)

```c
uint32 MESSAGE_VERSION = 1

uint64 timestamp				# 系统启动后的时间（微秒）

float32 indicated_airspeed_m_s			# [m/s] 指示空速（IAS），若无效则设置为NAN
float32 calibrated_airspeed_m_s     		# [m/s] 校准空速（CAS），若无效则设置为NAN
float32 true_airspeed_m_s			# [m/s] 真空速（TAS），若无效则设置为NAN

int8 airspeed_source				# 当前发布的空速值来源
int8 DISABLED = -1
int8 GROUND_MINUS_WIND = 0
int8 SENSOR_1 = 1
int8 SENSOR_2 = 2
int8 SENSOR_3 = 3
int8 SYNTHETIC = 4

# 调试状态
float32 calibrated_ground_minus_wind_m_s 	# 基于零侧滑假设估算的风速计算的校准空速，若无效则设置为NAN
float32 calibraded_airspeed_synth_m_s		# 合成空速（米每秒），若无效则设置为NAN
float32 airspeed_derivative_filtered		# 滤波后的指示空速导数 [m/s/s]
float32 throttle_filtered			# 滤波后的固定翼油门 [-]
float32 pitch_filtered				# 滤波后的俯仰角 [rad]

```