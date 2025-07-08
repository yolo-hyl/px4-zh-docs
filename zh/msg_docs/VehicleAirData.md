# 机体气动数据（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAirData.msg)

```c

uint64 timestamp            # 系统启动后经过的时间（微秒）

uint64 timestamp_sample     # 原始数据的时间戳（微秒）

uint32 baro_device_id       # 选定气压计的唯一设备ID

float32 baro_alt_meter			# 使用修正海平面气压（SENS_BARO_QNH）和ISA模型计算的气压高度（米） 
float32 baro_pressure_pa		# 绝对压力（帕斯卡）
float32 ambient_temperature		# 环境温度（摄氏度）
uint8 temperature_source		# 温度数据来源：0: 默认温度（15°C），1: 外部气压计，2: 空速

float32 rho				# 空气密度

uint8 calibration_count     # 校准变更计数器。每次校准变更时单调递增。

```