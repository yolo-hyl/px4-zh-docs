# RoverVelocityStatus（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverVelocityStatus.msg)

```c
uint64 timestamp # 系统启动后时间（微秒）

float32 measured_speed_body_x          # [m/s] 机体x轴方向实测速度。正：前进，负：后退
float32 adjusted_speed_body_x_setpoint # [m/s] 机体x轴方向速率限制后的速度设定值。正：前进，负：后退
float32 pid_throttle_body_x_integral   # 机体x轴速度闭环控制器的PID积分项
float32 measured_speed_body_y          # [m/s] 机体y轴方向实测速度。正：右，负：左（仅适用于Mecanum）
float32 adjusted_speed_body_y_setpoint # [m/s] 机体y轴方向速率限制后的速度设定值。正：右，负：左（仅适用于Mecanum）
float32 pid_throttle_body_y_integral   # 机体y轴速度闭环控制器的PID积分项（仅适用于Mecanum）
```