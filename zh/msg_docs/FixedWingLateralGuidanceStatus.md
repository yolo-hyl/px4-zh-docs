# 固定翼横向引导状态(UORB消息)

固定翼横向引导状态消息
由fw_pos_control模块发布，用于报告横向设定点和NPFG调试输出

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FixedWingLateralGuidanceStatus.msg)

```c
# 固定翼横向引导状态消息
# 由fw_pos_control模块发布，用于报告横向设定点和NPFG调试输出

uint64 timestamp                # 系统启动后时间（微秒）

float32 course_setpoint         # [rad] [@range -pi, pi] 相对于（真实）北方的地面期望行进方向，由引导律设定
float32 lateral_acceleration_ff # [m/s^2] [FRD] 仅用于保持曲率的横向加速度需求
float32 bearing_feas            # [@range 0,1] 方位可行性
float32 bearing_feas_on_track   # [@range 0,1] 轨迹上的方位可行性
float32 signed_track_error      # [m] 带符号的轨迹误差
float32 track_error_bound       # [m] 轨迹误差边界
float32 adapted_period          # [s] 自适应周期（若启用自调谐）
uint8 wind_est_valid            # [boolean] true = 风速估计有效且/或被控制器使用（也指示即使有效是否禁用风速估计使用）

```