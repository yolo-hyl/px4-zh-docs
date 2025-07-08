# EstimatorSensorBias（UORB消息）

以国际单位制形式表示的传感器读数和运行中的偏差。传感器读数已补偿静态偏移、比例误差、运行中偏差和热漂移（如果启用了热补偿且可用）。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EstimatorSensorBias.msg)

```c
#
# 以国际单位制形式表示的传感器读数和运行中的偏差。传感器读数已补偿静态偏移、
# 比例误差、运行中偏差和热漂移（如果启用了热补偿且可用）。
#

uint64 timestamp                # 系统启动后经过的时间（微秒）
uint64 timestamp_sample         # 原始数据的时间戳（微秒）

# 运行中的偏差估计值（从未经补偿的数据中减去）

uint32 gyro_device_id           # 在不同电源周期间不变的传感器唯一设备ID
float32[3] gyro_bias            # 机体坐标系中的陀螺仪运行中偏差（rad/s）
float32 gyro_bias_limit         # 机体坐标系中最大陀螺仪运行中偏差的幅度（rad/s）
float32[3] gyro_bias_variance
bool gyro_bias_valid
bool gyro_bias_stable		# 当陀螺仪偏差估计值足够稳定可用于校准时为true

uint32 accel_device_id          # 在不同电源周期间不变的传感器唯一设备ID
float32[3] accel_bias           # 机体坐标系中的加速度计运行中偏差（m/s^2）
float32 accel_bias_limit        # 机体坐标系中最大加速度计运行中偏差的幅度（m/s^2）
float32[3] accel_bias_variance
bool accel_bias_valid
bool accel_bias_stable		# 当加速度计偏差估计值足够稳定可用于校准时为true

uint32 mag_device_id            # 在不同电源周期间不变的传感器唯一设备ID
float32[3] mag_bias             # 机体坐标系中的磁力计运行中偏差（Gauss）
float32 mag_bias_limit          # 机体坐标系中最大磁力计运行中偏差的幅度（Gauss）
float32[3] mag_bias_variance
bool mag_bias_valid
bool mag_bias_stable		# 当磁力计偏差估计值足够稳定可用于校准时为true
```