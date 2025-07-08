# 估计器状态 (UORB 消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatus.msg)

```c
uint64 timestamp		# 系统启动后经过的时间 (微秒)
uint64 timestamp_sample         # 原始数据的时间戳 (微秒)

float32[3] output_tracking_error # 返回包含输出预测器的角度、速度和位置跟踪误差幅度的向量 (rad)，(m/s)，(m)

uint16 gps_check_fail_flags     # 表示GPS检查状态的位掩码 - 请参见下文定义
```# 当对应测试失败时位为真
uint8 GPS_CHECK_FAIL_GPS_FIX = 0		# 0 : 固定类型不足 (无3D解)
uint8 GPS_CHECK_FAIL_MIN_SAT_COUNT = 1		# 1 : 最小卫星数量要求失败
uint8 GPS_CHECK_FAIL_MAX_PDOP = 2		# 2 : 最大允许PDOP失败
uint8 GPS_CHECK_FAIL_MAX_HORZ_ERR = 3		# 3 : 最大允许水平位置误差失败
uint8 GPS_CHECK_FAIL_MAX_VERT_ERR = 4		# 4 : 最大允许垂直位置误差失败
uint8 GPS_CHECK_FAIL_MAX_SPD_ERR = 5		# 5 : 最大允许速度误差失败
uint8 GPS_CHECK_FAIL_MAX_HORZ_DRIFT = 6		# 6 : 允许的最大水平位置漂移失败 - 需要静止的机体
uint8 GPS_CHECK_FAIL_MAX_VERT_DRIFT = 7		# 7 : 允许的最大垂直位置漂移失败 - 需要静止的机体
uint8 GPS_CHECK_FAIL_MAX_HORZ_SPD_ERR = 8	# 8 : 允许的最大水平速度失败 - 需要静止的机体
uint8 GPS_CHECK_FAIL_MAX_VERT_SPD_ERR = 9	# 9 : 允许的最大垂直速度差异失败
uint8 GPS_CHECK_FAIL_SPOOFED = 10		# 10 : GPS信号被欺骗

uint64 control_mode_flags	# 位掩码表示EKF逻辑状态
uint8 CS_TILT_ALIGN = 0		# 0 - 当滤波器姿态对齐完成时为真
uint8 CS_YAW_ALIGN = 1		# 1 - 当滤波器偏航对齐完成时为真
uint8 CS_GNSS_POS = 2		# 2 - 当正在融合GNSS位置测量时为真
uint8 CS_OPT_FLOW = 3		# 3 - 当正在融合光流测量时为真
uint8 CS_MAG_HDG = 4		# 4 - 当正在融合简单磁偏航角度时为真
uint8 CS_MAG_3D = 5		# 5 - 当正在融合3轴磁力计测量时为真
uint8 CS_MAG_DEC = 6		# 6 - 当正在融合合成磁偏角测量时为真
uint8 CS_IN_AIR = 7		# 7 - 当判定处于空中时为真
uint8 CS_WIND = 8		# 8 - 当正在估算风速时为真
uint8 CS_BARO_HGT = 9		# 9 - 当正在融合气压计数据时为真
uint8 CS_RNG_HGT = 10		# 10 - 当正在融合测距仪数据用于高度辅助时为真
uint8 CS_GPS_HGT = 11		# 11 - 当正在融合GPS高度时为真
uint8 CS_EV_POS = 12		# 12 - 当正在融合外部视觉的本地位置数据时为真
uint8 CS_EV_YAW = 13		# 13 - 当正在融合外部视觉的偏航数据时为真
uint8 CS_EV_HGT = 14		# 14 - 当正在融合外部视觉的高度数据时为真
uint8 CS_BETA = 15		# 15 - 当正在融合合成侧滑测量时为真
uint8 CS_MAG_FIELD = 16		# 16 - 当磁力计仅更新磁场状态时为真
uint8 CS_FIXED_WING = 17	# 17 - 当判定为固定翼机体并具有约束侧滑时为真
uint8 CS_MAG_FAULT = 18		# 18 - 当磁力计被判定故障且不再使用时为真
uint8 CS_ASPD = 19		# 19 - 当正在融合空速测量时为真
uint8 CS_GND_EFFECT = 20	# 20 - 当地面效应引起的静压升高保护激活时为真
uint8 CS_RNG_STUCK = 21		# 21 - 当检测到测距仪传感器卡顿时为真
uint8 CS_GPS_YAW = 22		# 22 - 当正在融合GPS接收器的偏航(非地面航向)数据时为真
uint8 CS_MAG_ALIGNED = 23	# 23 - 当飞行中磁场对齐完成时为真
uint8 CS_EV_VEL = 24		# 24 - 当计划融合外部视觉的本地坐标系速度数据时为真
uint8 CS_SYNTHETIC_MAG_Z = 25	# 25 - 当正在使用合成磁力计Z分量测量时为真
uint8 CS_VEHICLE_AT_REST = 26	# 26 - 当机体静止时为真
uint8 CS_GPS_YAW_FAULT = 27	# 27 - 当GNSS航向被判定故障且不再使用时为真
uint8 CS_RNG_FAULT = 28		# 28 - 当测距仪被判定故障且不再使用时为真
uint8 CS_GNSS_VEL = 44		# 44 - 当正在融合GNSS速度测量时为真

uint32 filter_fault_flags	# 位掩码表示EKF内部故障# 0 - 如果磁力计X轴的融合遇到数值错误则为真# 1 - 为真时，表示磁力计Y轴的数据融合遇到了数值错误# 2 - 为真时表明磁力计Z轴融合过程中出现了数值错误# 3 - true if 磁航向融合过程中出现数值错误# 4 - 如果磁偏角的融合遇到数值错误时为真# 5 - 如果空速融合过程中发生数值错误，则为 true# 6 - 如果合成侧滑约束的融合过程中遇到数值错误，则为真# 7 - 如果光流X轴的融合出现数值错误，则为true# 8 - 当光流Y轴的融合遇到数值错误时为真# 9 - 若北向速度融合出现数值错误，则为真# 10 - true 如果东向速度融合过程中遇到数值误差# 11 - true 当向下速度融合时遇到数值错误时为真# 12 - 如果北向位置融合遇到数值错误，则为 true# 13 - 如果东向位置的融合过程中出现数值错误，则为真# 14 - 如果向下位置的融合遇到了数值错误，则为 true# 15 - 如果检测到差的速度差偏差估计，则为true# 16 - 当检测到垂直加速度计数据异常时为真# 17 - 为真时表明 delta 速度数据包含裁剪（非对称限制）

float32 pos_horiz_accuracy # 1-Sigma 估计的水平位置精度（相对于估计器原点）(m)
float32 pos_vert_accuracy # 1-Sigma 估计的垂直位置精度（相对于估计器原点）(m)

float32 hdg_test_ratio # 低通滤波后的最大航向创新分量与创新检测阈值的比例
float32 vel_test_ratio # 低通滤波后的最大速度创新分量与创新检测阈值的比例
float32 pos_test_ratio # 低通滤波后的最大水平位置创新分量与创新检测阈值的比例
float32 hgt_test_ratio # 低通滤波后的垂直位置创新与创新检测阈值的比例
float32 tas_test_ratio # 低通滤波后的真空速创新与创新检测阈值的比例
float32 hagl_test_ratio # 低通滤波后的地高创新与创新检测阈值的比例
float32 beta_test_ratio # 低通滤波后的合成侧滑角创新与创新检测阈值的比例

uint16 solution_status_flags # 位掩码，指示哪些滤波器运动学状态输出可用于飞行控制# 0 - 如果姿态估计是好的则为True# 1 - 如果水平速度估计良好则为真# 2 - 如果垂直速度估计良好则为真# 3 - 如果水平位置（相对）估计良好则为真# 4 - 若水平位置（绝对）估计良好则为真# 5 - True if the vertical position (absolute) estimate is good

# 5 - 垂直位置（绝对）估计良好时为真# 6 - 如果垂直位置（地面以上）估计良好，则为真# 7 - True if the EKF is in a constant position mode and is not using external measurements (eg GPS or optical flow)

如果EKF处于恒定位置模式且未使用外部测量（例如GPS或光流），则为True。# 8 - 当EKF具有足够数据进入能提供（相对）位置估计的模式时为真# 9 - 如果EKF有足够的数据进入能够提供（绝对）位置估计的模式，则为True# 10 - 如果EKF检测到GPS异常，则为True# 11 - 如果EKF检测到加速度计数据异常则为真

uint8 reset_count_vel_ne # 水平位置重置事件计数（允许在计数超过255时循环）
uint8 reset_count_vel_d  # 垂直速度重置事件计数（允许在计数超过255时循环）
uint8 reset_count_pos_ne # 水平位置重置事件计数（允许在计数超过255时循环）
uint8 reset_count_pod_d  # 垂直位置重置事件计数（允许在计数超过255时循环）
uint8 reset_count_quat   # 四元数重置事件计数（允许在计数超过255时循环）

float32 time_slip # EKF惯性计算相对于系统时间的累计滑动时间（秒）

bool pre_flt_fail_innov_heading
bool pre_flt_fail_innov_height
bool pre_flt_fail_innov_pos_horiz
bool pre_flt_fail_innov_vel_horiz
bool pre_flt_fail_innov_vel_vert
bool pre_flt_fail_mag_field_disturbed

uint32 accel_device_id
uint32 gyro_device_id
uint32 baro_device_id
uint32 mag_device_id# 传统本地位置估计器（LPE）标志
uint8 health_flags		# 位掩码，用于指示传感器健康状态（速度、位置、高度）
uint8 timeout_flags		# 位掩码，用于指示超时标志（速度、位置、高度）

float32 mag_inclination_deg
float32 mag_inclination_ref_deg
float32 mag_strength_gs
float32 mag_strength_ref_gs