# FixedWingLateralSetpoint (UORB消息)

固定翼横向设定点消息
由fw_lateral_longitudinal_control模块使用
航向、空速方向或横向加速度中至少一项必须为有限值。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/FixedWingLateralSetpoint.msg)

```c
# 固定翼横向设定点消息
# 由fw_lateral_longitudinal_control模块使用
# 航向、空速方向或横向加速度中至少一项必须为有限值

uint32 MESSAGE_VERSION = 0

uint64 timestamp                        # 系统启动后的时间（微秒）

float32 course 				# [rad] [@range -pi, pi] 相对于（真）北的方向设定值（地面航向）。未直接控制时为NAN。
float32 airspeed_direction    		# [rad] [@range -pi, pi] 空速矢量相对于（真）北的水平角度设定值。无侧滑时等同于机体航向。未直接控制时为NAN，有限值时优先级高于航向设定。
float32 lateral_acceleration 		# [m/s^2] [FRD] 横向加速度设定值。未直接控制时为NAN，当航向或空速方向设定值为有限值时用作前馈。

```