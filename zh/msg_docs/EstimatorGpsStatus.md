# EstimatorGpsStatus (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorGpsStatus.msg)  

```c  
uint64 timestamp                          # 系统启动后经过的时间（微秒）  
uint64 timestamp_sample                   # 原始数据的时间戳（微秒）  

bool checks_passed  

bool check_fail_gps_fix          # 0 : 定位类型不足（无3D定位）  
bool check_fail_min_sat_count    # 1 : 最小卫星数检查失败  
bool check_fail_max_pdop         # 2 : 允许的最大PDOP值检查失败  
bool check_fail_max_horz_err     # 3 : 允许的最大水平位置误差检查失败  
bool check_fail_max_vert_err     # 4 : 允许的最大垂直位置误差检查失败  
bool check_fail_max_spd_err      # 5 : 允许的最大速度误差检查失败  
bool check_fail_max_horz_drift   # 6 : 允许的最大水平位置漂移检查失败 - 需要机体静止  
bool check_fail_max_vert_drift   # 7 : 允许的最大垂直位置漂移检查失败 - 需要机体静止  
bool check_fail_max_horz_spd_err # 8 : 允许的最大水平速度检查失败 - 需要机体静止  
bool check_fail_max_vert_spd_err # 9 : 允许的最大垂直速度差异检查失败  
bool check_fail_spoofed_gps      # 10 : GPS信号被欺骗  

float32 position_drift_rate_horizontal_m_s # 水平位置速率幅值（米/秒）  
float32 position_drift_rate_vertical_m_s   # 垂直位置速率幅值（米/秒）  
float32 filtered_horizontal_speed_m_s      # 滤波后的水平速度幅值（米/秒）  
```