# ButtonEvent (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ButtonEvent.msg)

```c
uint64 timestamp			# 系统启动后经过的时间（微秒）
bool triggered				# 如果事件被触发则设为true

# TOPICS button_event safety_button

uint8 ORB_QUEUE_LENGTH = 2

```