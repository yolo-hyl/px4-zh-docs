# GpioOut (UORB message)

GPIO掩码和状态

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GpioOut.msg)

```c
# GPIO mask and state

uint64 timestamp			# 系统启动以来的时间（微秒）
uint32 device_id			# 设备ID

uint32 mask				# 引脚掩码
uint32 state				# 引脚状态掩码

```