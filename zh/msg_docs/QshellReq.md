# QshellReq (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/QshellReq.msg)

```c
uint64 timestamp		# 系统启动后的时间（微秒）
char[100] cmd
uint32 MAX_STRLEN = 100
uint32 strlen
uint32 request_sequence

```