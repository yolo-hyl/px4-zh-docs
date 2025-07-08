# MavlinkLog (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MavlinkLog.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）

char[127] text
uint8 severity # 日志级别（与Linux内核相同，从0开始）

uint8 ORB_QUEUE_LENGTH = 8

```