# FollowTargetEstimator（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FollowTargetEstimator.msg)

```c
uint64 timestamp                     # 系统启动以来的时间（微秒）
uint64 last_filter_reset_timestamp   # 最后一次滤波器重置的时间（微秒）

bool valid              # 估计器状态是否可用
bool stale              # 估计器是否已停止接收follow_target消息一段时间。估计值可能仍有效，但精度可能下降。

float64 lat_est         # 目标纬度估计值
float64 lon_est         # 目标经度估计值
float32 alt_est         # 目标高度估计值

float32[3] pos_est      # 目标NED坐标系位置估计值（米）
float32[3] vel_est      # 目标NED坐标系速度估计值（米/秒）
float32[3] acc_est      # 目标NED坐标系加速度估计值（米²/秒）

uint64 prediction_count
uint64 fusion_count

```