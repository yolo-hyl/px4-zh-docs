# FollowTargetStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FollowTargetStatus.msg)

```c
uint64 timestamp                  # [微秒] 系统启动后的时间

float32 tracked_target_course     # [rad] 在NED本地坐标系中追踪的目标航向（北为航向零）
float32 follow_angle              # [rad] 当前跟随角度设置

float32 orbit_angle_setpoint      # [rad] 来自平滑轨迹生成器的当前轨道角度设定值
float32 angular_rate_setpoint     # [rad/s] 来自加加速度限制轨道角轨迹的角速率指令

float32[3] desired_position_raw   # [m] 原始"理想"期望无人机位置（假设无人机可瞬间移动）

bool in_emergency_ascent          # [bool] 当进行紧急上升时为真（当与地面距离低于安全高度时）
float32 gimbal_pitch              # [rad] 为在画面中心追踪目标而指令的云台俯仰角

```