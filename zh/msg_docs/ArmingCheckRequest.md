# ArmingCheckRequest (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ArmingCheckRequest.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动后经过的时间（微秒）

# 广播消息，要求所有注册的arming检查进行报告

uint8 request_id

```