# 传感器选择（UORB消息）

用于在 sensor_combined 主题中输出的投票传感器 ID。
将在传感器模块启动时以及传感器选择变更时更新

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorSelection.msg)

```c
#
# Sensor ID's for the voted sensors output on the sensor_combined topic.
# Will be updated on startup of the sensor module and when sensor selection changes
#
uint64 timestamp		# time since system start (microseconds)
uint32 accel_device_id		# unique device ID for the selected accelerometers
uint32 gyro_device_id		# unique device ID for the selected rate gyros

```