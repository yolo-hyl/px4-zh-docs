# LandingGearWheel (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LandingGearWheel.msg)

```c
uint64 timestamp # 系统启动后的时间（微秒）

float32 normalized_wheel_setpoint	# 负值表示左转，正值表示右转 [-1, 1]
```