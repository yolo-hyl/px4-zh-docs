# GpioIn (UORB消息)

GPIO 掩码和状态

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GpioIn.msg)

```c
# GPIO mask and state

uint64 timestamp			# time since system start (microseconds)
uint32 device_id			# Device id

uint32 state				# pin state mask

```