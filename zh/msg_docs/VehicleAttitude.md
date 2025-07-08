# 机体姿态 (UORB消息)

这类似于mavlink消息ATTITUDE_QUATERNION，但用于机载用途
四元数采用Hamilton convention，顺序为q(w, x, y, z)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleAttitude.msg)

```c
# 这类似于mavlink消息ATTITUDE_QUATERNION，但用于机载用途
# 四元数采用Hamilton convention，顺序为q(w, x, y, z)

uint32 MESSAGE_VERSION = 0

uint64 timestamp                # 系统启动后时间戳(微秒)

uint64 timestamp_sample         # 原始数据的时间戳(微秒)

float32[4] q                    # 从FRD机体坐标系到NED地球坐标系的四元数旋转
float32[4] delta_q_reset        # 上次重置期间四元数的改变量
uint8 quat_reset_counter        # 四元数重置计数器

# TOPICS vehicle_attitude vehicle_attitude_groundtruth external_ins_attitude
# TOPICS estimator_attitude

```