# 传感器状态IMU (UORB message)

传感器检查指标。对于主传感器或未填充的传感器，该值为零。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorsStatusImu.msg)

```c
#
# 传感器检查指标。对于主传感器或未填充的传感器，该值为零。
#
uint64 timestamp # 系统启动以来的时间（微秒）

uint32 accel_device_id_primary       # 当前主加速度计设备ID（参考用）

uint32[4] 加速度计设备ID
float32[4] 加速度计不一致性_m_s_s # IMU实例与平均值之间的加速度差异幅度（m/s²）。
bool[4] 加速度计健康状态
uint8[4] 加速度计优先级

uint32 gyro_device_id_primary       # 当前主陀螺仪设备ID（参考用）

uint32[4] 陀螺仪设备ID
float32[4] 陀螺仪不一致性_rad_s # IMU实例与平均值之间的角速度差异幅度（rad/s）。
bool[4] 陀螺仪健康状态
uint8[4] 陀螺仪优先级

```