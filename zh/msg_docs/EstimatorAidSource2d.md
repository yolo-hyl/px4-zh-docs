# EstimatorAidSource2d (UORB消息)

[源文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorAidSource2d.msg)

```c
uint64 timestamp                # 系统启动后的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

uint8 estimator_instance

uint32 device_id

uint64 time_last_fuse

float64[2] observation
float32[2] observation_variance

float32[2] innovation
float32[2] innovation_filtered

float32[2] innovation_variance

float32[2] test_ratio           # 标准化的创新平方
float32[2] test_ratio_filtered  # 符号过滤的测试比率

bool innovation_rejected        # 观测值被拒绝时为true
bool fused                      # 成功融合时为true

# TOPICS estimator_aid_src_ev_pos estimator_aid_src_fake_pos estimator_aid_src_gnss_pos estimator_aid_src_aux_global_position
# TOPICS estimator_aid_src_aux_vel estimator_aid_src_optical_flow
# TOPICS estimator_aid_src_drag

```