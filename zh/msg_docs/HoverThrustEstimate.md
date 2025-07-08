# HoverThrustEstimate (UORB 消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/HoverThrustEstimate.msg)

```c
uint64 timestamp                # 系统启动后经过的时间（微秒）
uint64 timestamp_sample         # 此估计值最后一次使用的传感器数据时间戳

float32 hover_thrust		# 估计的悬停推力 [0.1, 0.9]
float32 hover_thrust_var	# 估计的悬停推力方差

float32 accel_innov		# 上次加速度融合的创新值
float32 accel_innov_var		# 上次加速度融合的创新方差
float32 accel_innov_test_ratio	# 归一化的创新平方检测比值

float32 accel_noise_var		# 从创新残差估计的垂直加速度噪声方差

bool valid

```