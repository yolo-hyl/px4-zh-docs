# RoverThrottleSetpoint（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverThrottleSetpoint.msg)

```c

uint64 timestamp        # 系统启动后的时间（微秒）

float32 throttle_body_x # 沿机体X轴的油门设定值 [-1, 1]（正值=向前，负值=向后）
float32 throttle_body_y # 沿机体Y轴的油门设定值 [-1, 1]（仅麦克纳姆轮，正值=右，负值=左）

```