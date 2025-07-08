# RoverPositionSetpoint（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RoverPositionSetpoint.msg)

```c
uint64 timestamp # 系统启动后时间（微秒）

float32[2] position_ned # 2维位置设定点在NED坐标系中 [m]
float32[2] start_ned	# （可选）2维起始位置在NED坐标系中，用于定义车辆追踪到position_ned的路径 [m]（默认为机体位置）
float32 cruising_speed  # （可选）指定巡航速度 [m/s]（默认为最大速度）
float32 arrival_speed   # （可选）指定到达速度 [m/s]（默认为零）

float32 yaw             # [rad] [-pi,pi] 从北向量开始。可选，未设置时为NAN。仅适用于麦克纳姆轮。默认为机体偏航角

```