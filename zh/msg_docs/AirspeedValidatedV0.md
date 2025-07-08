# AirspeedValidatedV0 (UORB message)



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/px4_msgs_old/msg/AirspeedValidatedV0.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp				# 系统启动后经过的时间（微秒）

float32 indicated_airspeed_m_s			# 指示空速（m/s）(IAS)，若无效则设置为NAN
float32 calibrated_airspeed_m_s     		# 校准空速（m/s）(CAS，已修正仪器误差)，若无效则设置为NAN
float32 true_airspeed_m_s			# 真实过滤空速（m/s）(TAS)，若无效则设置为NAN

float32 calibrated_ground_minus_wind_m_s 	# 由地速-风速计算的CAS（风速基于零侧滑假设估算），若无效则设置为NAN
float32 true_ground_minus_wind_m_s 		# 由地速-风速计算的TAS（风速基于零侧滑假设估算），若无效则设置为NAN

bool airspeed_sensor_measurement_valid 		# 若至少一个空速传感器数据有效则为True

int8 selected_airspeed_index 			# 1-3：空速传感器索引，0：地速-风速，-1：空速无效

float32 airspeed_derivative_filtered		# 过滤后的指示空速导数 [m/s/s]
float32 throttle_filtered			# 过滤后的固定翼油门 [-]
float32 pitch_filtered				# 过滤后的俯仰角 [rad]

```