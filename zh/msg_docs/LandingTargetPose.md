# LandingTargetPose (UORB消息)

导航（机体固定，北对齐，NED）和惯性（世界固定，北对齐，NED）坐标系中精准着陆目标的相对位置  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LandingTargetPose.msg)  

```c
# Relative position of precision land target in navigation (body fixed, north aligned, NED) and inertial (world fixed, north aligned, NED) frames

uint64 timestamp		# time since system start (microseconds)

bool is_static			# 标记着陆目标是否为静态或相对于地面运动

bool rel_pos_valid		# 标记相对位置是否有效
bool rel_vel_valid		# 标记相对速度是否有效

float32 x_rel			# 目标X/北方向相对机体的位置（导航坐标系） [米]
float32 y_rel			# 目标Y/东方向相对机体的位置（导航坐标系） [米]
float32 z_rel			# 目标Z/下方向相对机体的位置（导航坐标系） [米]

float32 vx_rel			# 目标X/北方向相对机体的速度（导航坐标系） [米/秒]
float32 vy_rel			# 目标Y/东方向相对机体的速度（导航坐标系） [米/秒]

float32 cov_x_rel		# X/北方向位置协方差 [米²]
float32 cov_y_rel		# Y/东方向位置协方差 [米²]

float32 cov_vx_rel		# X/北方向速度协方差 [(米/秒)²]
float32 cov_vy_rel		# Y/东方向速度协方差 [(米/秒)²]

bool abs_pos_valid		# 标记绝对位置是否有效
float32 x_abs			# 目标X/北方向相对原点的位置（导航坐标系） [米]
float32 y_abs			# 目标Y/东方向相对原点的位置（导航坐标系） [米]
float32 z_abs			# 目标Z/下方向相对原点的位置（导航坐标系） [米]

```