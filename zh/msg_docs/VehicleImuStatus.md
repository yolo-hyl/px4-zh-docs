# 机体IMU状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleImuStatus.msg)

```c
uint64 timestamp                # 自系统启动以来的时间（微秒）

uint32 accel_device_id          # 不会因电源循环而改变的传感器唯一设备ID
uint32 gyro_device_id           # 不会因电源循环而改变的传感器唯一设备ID

uint32[3] accel_clipping        # 各轴的总剪切
uint32[3] gyro_clipping         # 各轴的总剪切

uint32 accel_error_count
uint32 gyro_error_count

float32 accel_rate_hz
float32 gyro_rate_hz

float32 accel_raw_rate_hz       # 完整原始传感器采样率（Hz）
float32 gyro_raw_rate_hz        # 完整原始传感器采样率（Hz）

float32 accel_vibration_metric  # 加速度计数据中的高频振动水平（m/s/s）
float32 gyro_vibration_metric   # 陀螺仪数据中的高频振动水平（rad/s）
float32 delta_angle_coning_metric # 平均IMU角增量锥形校正（rad^2）

float32[3] mean_accel           # 自上次发布以来的平均加速度计读数
float32[3] mean_gyro            # 自上次发布以来的平均陀螺仪读数
float32[3] var_accel            # 自上次发布以来的加速度计方差
float32[3] var_gyro             # 自上次发布以来的陀螺仪方差

float32 temperature_accel
float32 temperature_gyro

```