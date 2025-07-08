# FixedWingLongitudinalSetpoint (UORB消息)

固定翼纵向设定点消息  
由fw_lateral_longitudinal_control模块使用  
如果pitch_direct和throttle_direct都不是有限值，则控制器依赖于高度/高度率和等效空速来控制垂直运动  
如果高度和高度率都是NAN，控制器将保持当前高度  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/FixedWingLongitudinalSetpoint.msg)

```c
# 固定翼纵向设定点消息
# 由fw_lateral_longitudinal_control模块使用
# 如果pitch_direct和throttle_direct都不是有限值，则控制器依赖于高度/高度率和等效空速来控制垂直运动
# 如果高度和高度率都是NAN，控制器将保持当前高度

uint32 MESSAGE_VERSION = 0

uint64 timestamp                        # 系统启动后经过的时间（微秒）

float32 altitude  			# [m] 高度设定值（相对于海平面，AMSL），若为NAN或高度率为有限值则不直接控制
float32 height_rate 			# [m/s] [ENU] 标量高度率设定值。若不直接控制则为NAN
float32 equivalent_airspeed 		# [m/s] [@range 0, inf] 标量等效空速设定值。若使用系统默认值则为NAN
float32 pitch_direct 			# [rad] [@range -pi, pi] [FRD] 若不控制则为NAN，优先于总能量控制器
float32 throttle_direct 		# [norm] [@range 0,1] 若不控制则为NAN，优先于总能量控制器
```