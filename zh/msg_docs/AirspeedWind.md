# 空速风速 (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/AirspeedWind.msg)  

```c  
uint64 timestamp		# 系统启动后时间戳（微秒）  
uint64 timestamp_sample         # 原始数据时间戳（微秒）  

float32 windspeed_north		# 风速北向/X方向分量（米/秒）  
float32 windspeed_east		# 风速东向/Y方向分量（米/秒）  

float32 variance_north		# 北向/X方向风速估计误差方差（米/秒）² - 未进行估计时设为零（无不确定性）  
float32 variance_east		# 东向/Y方向风速估计误差方差（米/秒）² - 未进行估计时设为零（无不确定性）  

float32 tas_innov 		# 真实空速创新值  
float32 tas_innov_var 		# 真实空速创新值方差  

float32 tas_scale_raw 		# 估计的真实空速缩放因子（未经过验证）  
float32 tas_scale_raw_var 	# 真实空速缩放因子方差  

float32 tas_scale_validated	# 验证后的估计真实空速缩放因子  

float32 beta_innov 		# 侧滑测量创新值  
float32 beta_innov_var 		# 侧滑测量创新值方差  

uint8 source			# 风速估计源  

uint8 SOURCE_AS_BETA_ONLY = 0	# 仅基于合成侧滑融合的风速估计  
uint8 SOURCE_AS_SENSOR_1 = 1	# 合成侧滑与空速融合（来自第一个空速传感器数据）  
uint8 SOURCE_AS_SENSOR_2 = 2	# 合成侧滑与空速融合（来自第二个空速传感器数据）  
uint8 SOURCE_AS_SENSOR_3 = 3	# 合成侧滑与空速融合（来自第三个空速传感器数据）  
```