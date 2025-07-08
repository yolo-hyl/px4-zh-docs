# UlogStreamAck (UORB 消息)

确认之前发送的带有 NEED_ACK 标志的 ulog_stream 消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/UlogStreamAck.msg)

```c
# 确认之前发送的带有
# NEED_ACK 标志的 ulog_stream 消息

uint64 timestamp		# 系统启动后经过的时间（微秒）
int32 ACK_TIMEOUT = 50		# 等待确认的超时时间，超时后会重新发送消息 [毫秒]
int32 ACK_MAX_TRIES = 50	# 最大重试次数（重新）发送消息，每次等待 ACK_TIMEOUT 毫秒

uint16 msg_sequence

```