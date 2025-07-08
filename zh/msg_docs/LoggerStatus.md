# 日志记录状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LoggerStatus.msg)

```c
uint64 timestamp               # 系统启动后经过的时间（微秒）

uint8 LOGGER_TYPE_FULL    = 0  # 正常，完整大小的日志
uint8 LOGGER_TYPE_MISSION = 1  # 简化任务日志（例如用于地理标记）
uint8 type

uint8 BACKEND_FILE    = 1
uint8 BACKEND_MAVLINK = 2
uint8 BACKEND_ALL     = 3
uint8 backend

bool is_logging

float32 total_written_kb       # 已写入日志的总数据量（千字节）
float32 write_rate_kb_s        # 写入速率（千字节/秒）

uint32 dropouts                # 由于缓冲区溢出导致的失败写入次数
uint32 message_gaps            # 丢失的消息数

uint32 buffer_used_bytes       # 当前缓冲区占用大小（字节）
uint32 buffer_size_bytes       # 总缓冲区大小（字节）

uint8 num_messages
```