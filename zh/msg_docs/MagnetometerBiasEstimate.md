# 磁力计偏差估计 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MagnetometerBiasEstimate.msg)

```c
uint64 timestamp                # 系统启动以来的时间 (微秒)

float32[4] bias_x		# 所有传感器的估计X轴偏差
float32[4] bias_y		# 所有传感器的估计Y轴偏差
float32[4] bias_z		# 所有传感器的估计Z轴偏差

bool[4] valid			# 如果估计器已收敛则为true
bool[4] stable
```