# EstimatorStatusFlags (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorStatusFlags.msg)

```c
uint64 timestamp                          # 系统启动后的时间戳（微秒）
uint64 timestamp_sample                   # 原始数据的时间戳（微秒）


# 滤波器控制状态
uint32 control_status_changes # 滤波器控制状态（cs）变化次数
bool cs_tilt_align            # 0 - 当滤波器倾斜对齐完成时为真
bool cs_yaw_align             # 1 - 当滤波器偏航对齐完成时为真
bool cs_gnss_pos              # 2 - 当计划融合GNSS位置测量时为真
bool cs_opt_flow              # 3 - 当计划融合光学流测量时为真
bool cs_mag_hdg               # 4 - 当计划融合简单磁力偏航测量时为真
bool cs_mag_3d                # 5 - 当计划融合三轴磁力计测量时为真
bool cs_mag_dec               # 6 - 当计划融合合成磁偏角测量时为真
bool cs_in_air                # 7 - 当机体在空中时为真
bool cs_wind                  # 8 - 当正在估计风速时为真
bool cs_baro_hgt              # 9 - 当正在融合气压数据时为真
bool cs_rng_hgt               # 10 - 当正在融合测距仪数据用于高度辅助时为真
bool cs_gps_hgt               # 11 - 当正在融合GPS高度数据时为真
bool cs_ev_pos                # 12 - 当计划融合外部视觉的局部位置数据时为真
bool cs_ev_yaw                # 13 - 当计划融合外部视觉的偏航数据时为真
bool cs_ev_hgt                # 14 - 当正在融合外部视觉的高度数据时为真
bool cs_fuse_beta             # 15 - 当正在融合合成侧滑测量时为真
bool cs_mag_field_disturbed   # 16 - 当磁场强度与预期不符时为真
bool cs_fixed_wing            # 17 - 当机体以固定翼模式运行时为真
bool cs_mag_fault             # 18 - 当磁力计被判定为故障且不再使用时为真
bool cs_fuse_aspd             # 19 - 当正在融合空速测量时为真
bool cs_gnd_effect            # 20 - 当地面效应引起的静压上升防护激活时为真
bool cs_rng_stuck             # 21 - 当测距数据超过10秒未更新且新值变化不足时为真
bool cs_gnss_yaw              # 22 - 当计划融合GPS接收器的偏航（非地面航向）数据时为真
bool cs_mag_aligned_in_flight # 23 - 当飞行中磁场对齐已完成时为真
bool cs_ev_vel                # 24 - 当计划融合外部视觉的局部速度数据时为真
bool cs_synthetic_mag_z       # 25 - 当正在使用合成磁力计Z轴测量时为真
bool cs_vehicle_at_rest       # 26 - 当机体静止时为真
bool cs_gnss_yaw_fault        # 27 - 当GNSS航向被判定为故障且不再使用时为真
bool cs_rng_fault             # 28 - 当测距仪被判定为故障且不再使用时为真
bool cs_inertial_dead_reckoning # 29 - 当不再融合限制水平速度漂移的测量时为真
bool cs_wind_dead_reckoning     # 30 - 当导航依赖风速相对测量时为真
bool cs_rng_kin_consistent      # 31 - 当测距仪运动一致性检查通过时为真
bool cs_fake_pos                # 32 - 当正在融合虚假位置测量时为真
bool cs_fake_hgt                # 33 - 当正在融合虚假高度测量时为真
bool cs_gravity_vector          # 34 - 当正在融合重力矢量测量时为真
bool cs_mag                     # 35 - 当计划融合三轴磁力计测量（仅磁场状态）时为真
bool cs_ev_yaw_fault            # 36 - 当外部视觉航向被判定为故障且不再使用时为真
bool cs_mag_heading_consistent  # 37 - 当磁力计航向与滤波器判定为一致时为真
bool cs_aux_gpos                # 38 - 当计划融合辅助全局位置测量时为真
bool cs_rng_terrain             # 39 - 当正在融合测距仪地形数据时为真
bool cs_opt_flow_terrain        # 40 - 当正在融合光流地形数据时为真
bool cs_valid_fake_pos          # 41 - 当正在融合有效常量位置时为真
bool cs_constant_pos            # 42 - 当机体处于常量位置时为真
bool cs_baro_fault	        # 43 - 当当前气压计被判定为故障且不再使用时为真
bool cs_gnss_vel                # 44 - 当计划融合GNSS速度测量时为真

# 故障状态
uint32 fault_status_changes   # 滤波器故障状态（fs）变化次数
bool fs_bad_mag_x             # 0 - 当磁力计X轴融合出现数值错误时为真
bool fs_bad_mag_y             # 1 - 当磁力计Y轴融合出现数值错误时为真
bool fs_bad_mag_z             # 2 - 当磁力计Z轴融合出现数值错误时为真
bool fs_bad_hdg               # 3 - 当航向角融合出现数值错误时为真
bool fs_bad_mag_decl          # 4 - 当磁偏角融合出现数值错误时为真
bool fs_bad_airspeed          # 5 - 当空速融合出现数值错误时为真
bool fs_bad_sideslip          # 6 - 当合成侧滑约束融合出现数值错误时为真
bool fs_bad_optflow_x         # 7 - 当光流X轴融合出现数值错误时为真
bool fs_bad_optflow_y         # 8 - 当光流Y轴融合出现数值错误时为真
bool fs_bad_acc_vertical      # 10 - 当检测到垂直加速度计数据异常时为真
bool fs_bad_acc_clipping      # 11 - 当增量速度数据包含剪切（不对称限幅）时为真


# 创新测试故障
uint32 innovation_fault_status_changes    # 创新故障状态（拒绝）变化次数
bool reject_hor_vel                       # 0 - 当水平速度观测被拒绝时为真
bool reject_ver_vel                       # 1 - 当垂直速度观测被拒绝时为真
bool reject_hor_pos                       # 2 - 当水平位置观测被拒绝时为真
bool reject_ver_pos                       # 3 - 当垂直位置观测被拒绝时为真
bool reject_yaw                           # 7 - 当偏航观测被拒绝时为真
bool reject_airspeed                      # 8 - 当空速观测被拒绝时为真
bool reject_sideslip                      # 9 - 当合成侧滑观测被拒绝时为真
bool reject_hagl                          # 10 - 当地面高度观测被拒绝时为真
bool reject_optflow_x                     # 11 - 当X轴光流观测被拒绝时为真
bool reject_optflow_y                     # 12 - 当Y轴光流观测被拒绝时为真
```