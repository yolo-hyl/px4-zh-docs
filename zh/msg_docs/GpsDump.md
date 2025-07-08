# GpsDump (UORB消息)

此消息用于将原始GPS通信内容转储到日志中。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GpsDump.msg)

```c
# This message is used to dump the raw gps communication to the log.

uint64 timestamp # 系统启动后的时间（微秒）

uint8 instance   # GNSS接收器实例
uint8 len        # 数据长度，MSB位设置=发送到GPS设备的消息，
                 # 清除=来自设备的消息
uint8[79] data   # 需要写入日志的数据

uint8 ORB_QUEUE_LENGTH = 8

```