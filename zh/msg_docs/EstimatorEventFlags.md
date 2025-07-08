# EstimatorEventFlags (UORB消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorEventFlags.msg)

```c
uint64 timestamp                        # 系统启动后经过的时间(微秒)
uint64 timestamp_sample                 # 原始数据的时间戳(微秒)

# 信息事件
uint32 information_event_changes        # 信息事件变化次数
bool gps_checks_passed                  # 0 - GPS质量检查通过时为真
bool reset_vel_to_gps                   # 1 - 速度状态重置为GPS测量值时为真
bool reset_vel_to_flow                  # 2 - 速度状态使用光流测量值重置时为真
bool reset_vel_to_vision                # 3 - 速度状态重置为视觉系统测量值时为真
bool reset_vel_to_zero                  # 4 - 速度状态重置为零时为真
bool reset_pos_to_last_known            # 5 - 位置状态重置为已知最后位置时为真
bool reset_pos_to_gps                   # 6 - 位置状态重置为GPS测量值时为真
bool reset_pos_to_vision                # 7 - 位置状态重置为视觉系统测量值时为真
bool starting_gps_fusion                # 8 - 开始使用GPS测量值校正状态估计时为真
bool starting_vision_pos_fusion         # 9 - 开始使用视觉系统位置测量值校正状态估计时为真
bool starting_vision_vel_fusion         # 10 - 开始使用视觉系统速度测量值校正状态估计时为真
bool starting_vision_yaw_fusion         # 11 - 开始使用视觉系统偏航测量值校正状态估计时为真
bool yaw_aligned_to_imu_gps             # 12 - 过滤器将偏航重置为IMU和GPS数据推导估计时为真
bool reset_hgt_to_baro                  # 13 - 垂直位置状态重置为气压计测量值时为真
bool reset_hgt_to_gps                   # 14 - 垂直位置状态重置为GPS测量值时为真
bool reset_hgt_to_rng                   # 15 - 垂直位置状态重置为测距测量值时为真
bool reset_hgt_to_ev                    # 16 - 垂直位置状态重置为EV测量值时为真
```