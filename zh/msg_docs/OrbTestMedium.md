# OrbTestMedium (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/OrbTestMedium.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）
int32 val
uint8[64] junk
uint8 ORB_QUEUE_LENGTH = 16
# TOPICS orb_test_medium orb_test_medium_multi orb_test_medium_wrap_around orb_test_medium_queue orb_test_medium_queue_poll
```