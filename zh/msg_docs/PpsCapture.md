# PpsCapture（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PpsCapture.msg)

```c
uint64 timestamp			  # PPS捕获事件发生时系统启动后的时间（微秒）
uint64 rtc_timestamp		# PPS捕获事件时的修正GPS UTC时间戳
uint8  pps_rate_exceeded_counter # 当PPS间隔时间小于50ms时递增

```