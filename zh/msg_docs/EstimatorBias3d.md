# EstimatorBias3d (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorBias3d.msg)

```c
uint64 timestamp                # 系统启动后经过的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

uint32 device_id                # 不随断电变化的传感器唯一设备ID

float32[3] bias                 # 估计的气压高度偏差（米）
float32[3] bias_var             # 估计的气压高度偏差方差（平方米）

float32[3] innov                # 上次测量融合的创新值（米）
float32[3] innov_var            # 上次测量融合的创新方差（平方米）
float32[3] innov_test_ratio     # 归一化的创新平方测试比

# TOPICS estimator_bias3d
# TOPICS estimator_ev_pos_bias
```