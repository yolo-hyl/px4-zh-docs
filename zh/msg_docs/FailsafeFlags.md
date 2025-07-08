# 安全保护标志（UORB消息）

由上锁与健康检查设置的安全保护状态机输入标志

标志必须命名为：false == 无故障（例如：_invalid, _unhealthy, _lost）
标志注释被用作安全保护状态机模拟的标签

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FailsafeFlags.msg)

```c
# 由上锁与健康检查设置的安全保护状态机输入标志
#
# 标志必须命名为：false == 无故障（例如：_invalid, _unhealthy, _lost）
# 标志注释被用作安全保护状态机模拟的标签

uint64 timestamp				# 系统启动后经过的时间（微秒）

# 按模式要求
uint32 mode_req_angular_velocity
uint32 mode_req_attitude
uint32 mode_req_local_alt
uint32 mode_req_local_position
uint32 mode_req_local_position_relaxed
uint32 mode_req_global_position
uint32 mode_req_mission
uint32 mode_req_offboard_signal
uint32 mode_req_home_position
uint32 mode_req_wind_and_flight_time_compliance # 如果设置，当风速或飞行时间超过限制时无法进入模式
uint32 mode_req_prevent_arming    # 如果设置，在此模式下无法上锁
uint32 mode_req_manual_control
uint32 mode_req_other             # 其他要求（用于外部模式）

# 模式要求
bool angular_velocity_invalid         # 角速度无效
bool attitude_invalid                 # 姿态无效
bool local_altitude_invalid           # 本地高度无效
bool local_position_invalid           # 本地位置估计无效
bool local_position_invalid_relaxed   # 本地位置（精度要求降低）无效（例如使用光流飞行）
bool local_velocity_invalid           # 本地速度估计无效
bool global_position_invalid          # 全球位置估计无效
bool auto_mission_missing             # 无任务可用
bool offboard_control_signal_lost     # 机外控制信号丢失
bool home_position_invalid            # 无可用起始位置

# 控制链路
bool manual_control_signal_lost       # 手动控制（遥控器）信号丢失
bool gcs_connection_lost              # 地面站连接丢失

# 电池
uint8 battery_warning                 # 电池警告等级（参见BatteryStatus.msg）
bool battery_low_remaining_time       # 剩余飞行时间低
bool battery_unhealthy                # 电池不健康

# 其他
bool geofence_breached        	      # 越界保护（一处或多处）被突破
bool mission_failure                  # 任务失败
bool vtol_fixed_wing_system_failure   # 机体处于固定翼系统故障安全模式（四旋翼降落之后）
bool wind_limit_exceeded              # 风速限制被超过
bool flight_time_limit_exceeded       # 最大飞行时间被超过
bool position_accuracy_low            # 位置估计已低于阈值，但目前仍被判定有效
bool navigator_failure        	      # 导航器未能执行模式

# 故障检测器
bool fd_critical_failure              # 严重故障（姿态/高度限制被超过，或外部ATS）
bool fd_esc_arming_failure            # 电调未能上锁
bool fd_imbalanced_prop               # 检测到螺旋桨不平衡
bool fd_motor_failure                 # 电机故障
```