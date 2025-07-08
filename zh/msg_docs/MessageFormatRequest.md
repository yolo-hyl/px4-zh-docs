# MessageFormatRequest (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MessageFormatRequest.msg)

```c
uint64 timestamp # 系统启动后经过的时间（微秒）

# 向PX4请求获取消息的hash值，以检查消息兼容性

uint16 LATEST_PROTOCOL_VERSION = 1 # 此协议的当前版本。每当MessageFormatRequest或MessageFormatResponse发生变化时，增加此版本。

uint16 protocol_version           # 必须设置为LATEST_PROTOCOL_VERSION。请勿修改此字段，它必须是timestamp字段之后的第一个字段

char[50] topic_name  # 例如 /fmu/in/vehicle_command
```