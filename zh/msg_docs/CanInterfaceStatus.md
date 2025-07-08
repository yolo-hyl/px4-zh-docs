# CanInterfaceStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CanInterfaceStatus.msg)

```c
uint64 timestamp		# 系统启动后的时间（微秒）
uint8 interface

uint64 io_errors
uint64 frames_tx
uint64 frames_rx
```