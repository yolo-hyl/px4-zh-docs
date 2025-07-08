# 加热器状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/HeaterStatus.msg)

```c
uint64 timestamp	# 系统启动后经过的时间（微秒）

uint32 device_id

bool heater_on
bool temperature_target_met

float32 temperature_sensor
float32 temperature_target

uint32 controller_period_usec
uint32 controller_time_on_usec

float32 proportional_value
float32 integrator_value
float32 feed_forward_value

uint8 MODE_GPIO  = 1
uint8 MODE_PX4IO = 2
uint8 mode

```