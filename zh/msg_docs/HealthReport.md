# 健康报告 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/HealthReport.msg)

```c
uint64 timestamp # 系统启动后经过的时间（微秒）

uint64 can_arm_mode_flags              # 位字段，表示各飞行模式(NAVIGATION_STATE_*)是否允许上锁
uint64 can_run_mode_flags              # 位字段，表示各飞行模式是否可运行

uint64 health_is_present_flags         # 各健康组件(health_component_t)的存在状态标志
uint64 health_warning_flags
uint64 health_error_flags
# 当present==0且error==1时，表示组件缺失但必需

uint64 arming_check_warning_flags
uint64 arming_check_error_flags

```