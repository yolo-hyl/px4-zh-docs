# Ekf2Timestamps (UORB message)

此消息包含EKF2使用的传感器输入的(相对)时间戳。  
可用于可重复回放。

时间戳字段是EKF2的参考时间，并与sensor_combined主题的时间戳匹配。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Ekf2Timestamps.msg)

```c
# this message contains the (relative) timestamps of the sensor inputs used by EKF2.
# It can be used for reproducible replay.

# the timestamp field is the ekf2 reference time and matches the timestamp of
# the sensor_combined topic.

uint64 timestamp			 # 系统启动后的时间（微秒）

int16 RELATIVE_TIMESTAMP_INVALID = 32767 # (0x7fff) 如果相对时间戳中的某一个
                                         # 设置为此值，表示相关传感器数据未更新

# 时间戳相对于主时间戳，单位为0.1毫秒（timestamp +
# *_timestamp_rel = 绝对时间戳）。对于int16类型，最大允许与sensor_combined主题
# 的时间差为±3.2秒。

int16 airspeed_timestamp_rel
int16 airspeed_validated_timestamp_rel
int16 distance_sensor_timestamp_rel
int16 optical_flow_timestamp_rel
int16 vehicle_air_data_timestamp_rel
int16 vehicle_magnetometer_timestamp_rel
int16 visual_odometry_timestamp_rel

# 注意：这是一个高频率记录的主题，因此需要尽可能小

```