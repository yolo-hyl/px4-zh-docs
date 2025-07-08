# PurePursuitStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PurePursuitStatus.msg)

```c
uint64 timestamp # 系统启动后经过的时间(微秒)

float32 lookahead_distance   # [m] Pure Pursuit控制器的预瞄距离
float32 target_bearing       # [rad] Pure Pursuit控制器计算的目标方位角
float32 crosstrack_error     # [m] 机体到路径的最短距离(正：机体位于路径向量右侧，负：左侧)
float32 distance_to_waypoint # [m] 机体到当前航点的距离
float32 bearing_to_waypoint  # [rad] 当前航点的方位角

```