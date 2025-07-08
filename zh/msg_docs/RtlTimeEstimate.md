# RtlTimeEstimate（UORB消息）

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RtlTimeEstimate.msg)

```c
uint64 timestamp # 系统启动后的时间（微秒）

bool valid			# 表示时间估计值是否有效的标志
float32 time_estimate		# [s] 返回起飞点（RTL）的预估时间
float32 safe_time_estimate	# [s] 与time_estimate相同，但包含安全系数和安全余量（factor*t + margin）
```