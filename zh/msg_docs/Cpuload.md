# Cpuload（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Cpuload.msg)

```c
uint64 timestamp		# 系统启动后时间（微秒）
float32 load                    # 处理器负载（0到1之间）
float32 ram_usage		# RAM使用情况（0到1之间）

```