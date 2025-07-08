# EstimatorStates (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStates.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

float32[25] states		# 内部滤波器状态
uint8 n_states		# 实际使用的状态数量

float32[24] covariances	# 协方差矩阵的对角线元素
```