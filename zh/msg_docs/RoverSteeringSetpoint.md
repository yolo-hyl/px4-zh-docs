# RoverSteeringSetpoint (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverSteeringSetpoint.msg)

```c
uint64 timestamp # 系统启动后经过的时间（微秒）

float32 normalized_steering_angle # [-1, 1] 归一化的转向角度（仅限Ackermann转向，正值：向右转，负值：向左转）

float32 normalized_speed_diff     # [-1, 1] 月球车左右轮速度差的归一化值（仅限差速/麦卡纳姆轮，正值：向右转，负值：向左转）

```