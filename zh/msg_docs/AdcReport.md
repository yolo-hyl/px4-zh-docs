# AdcReport (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/AdcReport.msg)

```c
uint64 timestamp		# 系统启动后经过的时间（微秒）
uint32 device_id		# 传感器的唯一设备ID（断电重启后不变）
int16[12] channel_id		# ADC通道ID（负值表示无效），TODO: 应与数组索引保持一致
int32[12] raw_data		# ADC通道原始值（可接受负值），当channel ID为正值时有效
uint32 resolution		# ADC通道分辨率
float32 v_ref			# ADC通道参考电压（用于计算LSB电压，lsb=scale/resolution）
```