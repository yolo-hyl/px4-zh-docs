# FailureDetectorStatus（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FailureDetectorStatus.msg)

```c
uint64 timestamp                    # 系统启动后的时间（微秒）

# 故障检测状态
bool fd_roll
bool fd_pitch
bool fd_alt
bool fd_ext
bool fd_arm_escs
bool fd_battery
bool fd_imbalanced_prop
bool fd_motor

float32 imbalanced_prop_metric      # 螺旋桨不平衡检测的指标（低通滤波）
uint16 motor_failure_mask           # 电机索引的位掩码，表示电机严重故障

```