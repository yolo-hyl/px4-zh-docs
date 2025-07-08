# RoverRateStatus（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverRateStatus.msg)

```c
uint64 timestamp # 系统启动后的时间（微秒）

float32 measured_yaw_rate          # [rad/s] 测量的偏航率
float32 adjusted_yaw_rate_setpoint # [rad/s] 被跟踪的偏航率设定值（已应用速率变化限制）
float32 pid_yaw_rate_integral      # 闭环偏航率控制器的PID积分项

```