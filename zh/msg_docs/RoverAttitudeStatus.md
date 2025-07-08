# RoverAttitudeStatus (UORB message)



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverAttitudeStatus.msg)

```c
uint64 timestamp # 系统启动后经过的时间（微秒）

float32 measured_yaw          # [rad/s] 测量到的偏航率
float32 adjusted_yaw_setpoint # [rad/s] 正在跟踪的偏航设定值（已应用速率限制）

```