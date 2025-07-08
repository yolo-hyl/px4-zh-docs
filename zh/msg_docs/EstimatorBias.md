# EstimatorBias（UORB消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorBias.msg)  

```c
uint64 timestamp                # 自系统启动以来的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

uint32 device_id		# 传感器的唯一设备ID（断电后不变）
float32 bias			# 估计的气压高度偏置（米）
float32 bias_var		# 估计的气压高度偏置方差（平方米）

float32 innov			# 最后一次测量融合的创新值（米）
float32 innov_var		# 最后一次测量融合的创新方差（平方米）
float32 innov_test_ratio	# 归一化的创新平方测试比率

# TOPICS estimator_baro_bias estimator_gnss_hgt_bias
```