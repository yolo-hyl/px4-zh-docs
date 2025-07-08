# EstimatorInnovations (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorInnovations.msg)

```c
uint64 timestamp		# 系统启动后时间（微秒）
uint64 timestamp_sample         # 原始数据时间戳（微秒）

# GPS
float32[2] gps_hvel	# 水平GPS速度创新值（米/秒）和创新方差（(米/秒)**2）
float32    gps_vvel	# 垂直GPS速度创新值（米/秒）和创新方差（(米/秒)**2）
float32[2] gps_hpos	# 水平GPS位置创新值（米）和创新方差（米**2）
float32    gps_vpos	# 垂直GPS位置创新值（米）和创新方差（米**2）

# 外部视觉
float32[2] ev_hvel	# 水平外部视觉速度创新值（米/秒）和创新方差（(米/秒)**2）
float32    ev_vvel	# 垂直外部视觉速度创新值（米/秒）和创新方差（(米/秒)**2）
float32[2] ev_hpos	# 水平外部视觉位置创新值（米）和创新方差（米**2）
float32    ev_vpos	# 垂直外部视觉位置创新值（米）和创新方差（米**2）

# 高度传感器
float32 rng_vpos	# 距离传感器高度创新值（米）和创新方差（米**2）
float32 baro_vpos	# 气压计高度创新值（米）和创新方差（米**2）

# 辅助速度
float32[2] aux_hvel	# 来自着陆目标的水平辅助速度创新值（米/秒）和创新方差（(米/秒)**2）

# 光流
float32[2] flow		# 流量创新值（弧度/秒）和创新方差（(弧度/秒)**2）

# 其他
float32 heading		# 偏航角创新值（弧度）和创新方差（弧度**2）
float32[3] mag_field	# 地球磁场强度创新值（高斯）和创新方差（高斯**2）
float32[3] gravity	# 加速度计矢量重力创新值（米/秒**2）
float32[2] drag		# 拖曳特定力创新值（米/秒**2）和创新方差（(米/秒)**2）
float32 airspeed	# 空速创新值（米/秒）和创新方差（(米/秒)**2）
float32 beta		# 人工侧滑角创新值（弧度）和创新方差（弧度**2）
float32 hagl		# 地面高度创新值（米）和创新方差（米**2）
float32 hagl_rate	# 地面高度变化率创新值（米/秒）和创新方差（(米/秒)**2）

# 创新测试比率是标量值。如果字段是向量，
# 测试比率将放在向量的第一个分量中。

# TOPICS estimator_innovations estimator_innovation_variances estimator_innovation_test_ratios

```