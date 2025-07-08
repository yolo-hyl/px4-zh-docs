# EstimatorAidSource1d (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorAidSource1d.msg)

```c
uint64 timestamp             # 系统启动后经过的时间（微秒）
uint64 timestamp_sample      # 原始数据的时间戳（微秒）

uint8 estimator_instance

uint32 device_id

uint64 time_last_fuse

float32 observation
float32 observation_variance

float32 innovation
float32 innovation_filtered

float32 innovation_variance

float32 test_ratio           # 归一化后的创新平方
float32 test_ratio_filtered  # 带符号的过滤测试比率

bool innovation_rejected     # 如果观测值被拒绝则为true
bool fused                   # 如果样本成功融合则为true

# TOPICS estimator_aid_src_baro_hgt estimator_aid_src_ev_hgt estimator_aid_src_gnss_hgt estimator_aid_src_rng_hgt
# TOPICS estimator_aid_src_airspeed estimator_aid_src_sideslip
# TOPICS estimator_aid_src_fake_hgt
# TOPICS estimator_aid_src_gnss_yaw estimator_aid_src_ev_yaw

```