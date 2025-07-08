# 传感器校正 (UORB 消息)

投票传感器的国际单位制单位形式传感器校正

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorCorrection.msg)

```c
#
# 传感器校正的国际单位制单位形式，适用于投票传感器
#

uint64 timestamp		# 系统启动后的时间（微秒）

# 加速度计加速度输出的校正，其中 corrected_accel = raw_accel * accel_scale + accel_offset
# 注意校正值位于传感器坐标系中，必须在传感器数据旋转到机体坐标系前应用
uint32[4] accel_device_ids
float32[4] accel_temperature
float32[3] accel_offset_0	# 加速度计0在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] accel_offset_1	# 加速度计1在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] accel_offset_2	# 加速度计2在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] accel_offset_3	# 加速度计3在FRD板坐标系XYZ轴上的偏移值（m/s²）

# 陀螺仪角速度输出的校正，其中 corrected_rate = raw_rate * gyro_scale + gyro_offset
# 注意校正值位于传感器坐标系中，必须在传感器数据旋转到机体坐标系前应用
uint32[4] gyro_device_ids
float32[4] gyro_temperature
float32[3] gyro_offset_0	# 陀螺仪0在传感器坐标系XYZ轴上的偏移值（rad/s）
float32[3] gyro_offset_1	# 陀螺仪1在传感器坐标系XYZ轴上的偏移值（rad/s）
float32[3] gyro_offset_2	# 陀螺仪2在传感器坐标系XYZ轴上的偏移值（rad/s）
float32[3] gyro_offset_3	# 陀螺仪3在传感器坐标系XYZ轴上的偏移值（rad/s）

# 磁强计测量输出的校正，其中 corrected_mag = raw_mag * mag_scale + mag_offset
# 注意校正值位于传感器坐标系中，必须在传感器数据旋转到机体坐标系前应用
uint32[4] mag_device_ids
float32[4] mag_temperature
float32[3] mag_offset_0	# 磁强计0在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] mag_offset_1	# 磁强计1在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] mag_offset_2	# 磁强计2在FRD板坐标系XYZ轴上的偏移值（m/s²）
float32[3] mag_offset_3	# 磁强计3在FRD板坐标系XYZ轴上的偏移值（m/s²）

# 气压计压力输出的校正，其中 corrected_pressure = raw_pressure * pressure_scale + pressure_offset
# 注意校正值位于传感器坐标系中，必须在传感器数据旋转到机体坐标系前应用
uint32[4] baro_device_ids
float32[4] baro_temperature
float32 baro_offset_0		# 气压计0在传感器坐标系中的偏移值（帕斯卡）
float32 baro_offset_1		# 气压计1在传感器坐标系中的偏移值（帕斯卡）
float32 baro_offset_2		# 气压计2在传感器坐标系中的偏移值（帕斯卡）
float32 baro_offset_3		# 气压计3在传感器坐标系中的偏移值（帕斯卡）
```