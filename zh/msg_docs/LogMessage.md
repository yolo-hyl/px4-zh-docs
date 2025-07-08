# LogMessage (UORB消息)

一条日志消息，通过PX4_WARN、PX4_ERR、PX4_INFO输出

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LogMessage.msg)

```c
# 一条日志消息，通过PX4_WARN、PX4_ERR、PX4_INFO输出

uint64 timestamp		# 系统启动后经过的时间（微秒）

uint8 severity # 日志级别（与Linux内核一致，从0开始）
char[127] text

uint8 ORB_QUEUE_LENGTH = 4

```