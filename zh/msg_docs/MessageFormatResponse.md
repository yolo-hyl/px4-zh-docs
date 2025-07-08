# MessageFormatResponse (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MessageFormatResponse.msg)

```c
uint64 timestamp # 系统启动后时间戳(微秒)

# PX4返回的消息格式响应

uint16 protocol_version           # 必须设置为LATEST_PROTOCOL_VERSION。不要修改此字段，它必须是时间戳之后的第一个字段

char[50] topic_name  # 例如 /fmu/in/vehicle_command

bool success
uint32 message_hash # 所有消息字段的哈希值

```