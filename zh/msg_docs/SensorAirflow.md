# SensorAirflow (UORB message)



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorAirflow.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）
uint32 device_id                # 传感器设备的唯一ID（断电重启后不变）
float32 speed			# 风/气流传感器报告的速度
float32 direction		# 风/气流传感器报告的方向
uint8 status			# 传感器状态码

```