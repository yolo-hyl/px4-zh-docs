# PwmInput（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PwmInput.msg)

```c
uint64 timestamp   # 系统启动后经过的时间（微秒）
uint64 error_count # 定时器溢出捕获错误标志（AUX5或MAIN5）
uint32 pulse_width # 脉冲宽度，定时器计数值（微秒）
uint32 period      # 周期，定时器计数值（微秒）

```