# 风 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Wind.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

float32 windspeed_north		# 北向/X方向风速（米/秒）
float32 windspeed_east		# 东向/Y方向风速（米/秒）

float32 variance_north		# 北向/X方向风速估计误差方差（米/秒）² - 未进行估计时设置为零（无不确定性）
float32 variance_east		# 东向/Y方向风速估计误差方差（米/秒）² - 未进行估计时设置为零（无不确定性）

float32 tas_innov 		# 真空速创新值
float32 tas_innov_var 		# 真空速创新值方差

float32 beta_innov 		# 侧滑角测量创新值
float32 beta_innov_var 		# 侧滑角测量创新值方差

# TOPICS wind estimator_wind
```