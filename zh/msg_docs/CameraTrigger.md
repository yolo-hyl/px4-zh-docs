# 相机触发器 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CameraTrigger.msg)

```c
uint64 timestamp	# 自系统启动以来的时间（微秒）
uint64 timestamp_utc # UTC时间戳

uint32 seq		# 图像序列号
bool feedback	# 来自相机的触发反馈

uint32 ORB_QUEUE_LENGTH = 2

```