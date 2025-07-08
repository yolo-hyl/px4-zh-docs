# RoverVelocitySetpoint (UORB消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverVelocitySetpoint.msg)

```c
uint64 timestamp # 系统启动后时间戳（微秒）

float32 speed   # [m/s] [-inf, inf] 速度目标值（负值表示倒车）
float32 bearing # [rad] [-pi,pi] 从正北方向测量的角度。[无效值：NAN，速度定义在机体x轴方向]
float32 yaw     # [rad] [-pi, pi]（Mecanum专用，可选，默认为当前机体偏航角）NED坐标系下的机体偏航角目标值

```