# EstimatorAidSource3d（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorAidSource3d.msg)

```c
uint64 timestamp                # 系统启动以来的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

uint8 estimator_instance

uint32 device_id

uint64 time_last_fuse

float32[3] observation
float32[3] observation_variance

float32[3] innovation
float32[3] innovation_filtered

float32[3] innovation_variance

float32[3] test_ratio           # 归一化的创新平方
float32[3] test_ratio_filtered  # 有符号滤波后的测试比率

bool innovation_rejected        # 为true表示观测值已被拒绝
bool fused                      # 为true表示样本已成功融合

# TOPICS estimator_aid_src_ev_vel estimator_aid_src_gnss_vel estimator_aid_src_gravity estimator_aid_src_mag

```