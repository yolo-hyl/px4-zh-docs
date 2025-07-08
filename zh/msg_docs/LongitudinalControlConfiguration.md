# 纵向控制配置 (UORB消息)

固定翼纵向控制配置消息  
被fw_lateral_longitudinal_control模块和TECS使用，用于约束FixedWingLongitudinalSetpoint消息  
并配置生成的设定值。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/LongitudinalControlConfiguration.msg)

```c
# 固定翼纵向控制配置消息
# 被fw_lateral_longitudinal_control模块和TECS使用，用于约束FixedWingLongitudinalSetpoint消息
# 并配置生成的设定值。

uint32 MESSAGE_VERSION = 0

uint64 timestamp                        # 系统启动后经过的时间(微秒)

float32 pitch_min   			# [rad][@range -pi, pi] 如果为NAN，则默认使用FW_P_LIM_MIN的值。
float32 pitch_max   			# [rad][@range -pi, pi] 如果为NAN，则默认使用FW_P_LIM_MAX的值。
float32 throttle_min 			# [norm] [@range 0,1] 如果为NAN，则默认使用FW_THR_MIN的值。
float32 throttle_max 			# [norm] [@range 0,1] 如果为NAN，则默认使用FW_THR_MAX的值。
float32 climb_rate_target 		# [m/s] 改变高度的目标爬升率。如果为NAN，默认使用FW_T_CLIMB_MAX。FixedWingLongitudinalSetpoint中直接设置height_rate时不会使用此值。
float32 sink_rate_target 		# [m/s] 改变高度的目标下降率。如果为NAN，默认使用FW_T_SINK_MAX。FixedWingLongitudinalSetpoint中直接设置height_rate时不会使用此值。
float32 speed_weight 			# [@range 0,2] 0=俯仰仅控制高度，2=俯仰仅控制空速
bool enforce_low_height_condition 	# [boolean] 如果为真，高度控制器将使用替代时间常数以实现更精确的高度跟踪
bool disable_underspeed_protection 	# [boolean] 如果为真，高度控制器将禁用低速保护功能
```