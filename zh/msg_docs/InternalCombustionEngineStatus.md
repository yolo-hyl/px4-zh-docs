# 内燃机状态 (UORB消息)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/InternalCombustionEngineStatus.msg)  

```c  
uint64 timestamp					# 系统启动以来的时间（微秒）  

uint8 STATE_STOPPED = 0					# 发动机未运行。这是默认状态。  
uint8 STATE_STARTING = 1				# 发动机正在启动。这是一个瞬态状态。  
uint8 STATE_RUNNING = 2					# 发动机正常运行。  
uint8 STATE_FAULT = 3					# 发动机无法再运行。  
uint8 state  

uint32 FLAG_GENERAL_ERROR = 1				# 通用错误。  

uint32 FLAG_CRANKSHAFT_SENSOR_ERROR_SUPPORTED = 2	# 曲轴传感器错误。此标志是可选的。  
uint32 FLAG_CRANKSHAFT_SENSOR_ERROR = 4  

uint32 FLAG_TEMPERATURE_SUPPORTED = 8			# 温度级别。这些标志是可选的  
uint32 FLAG_TEMPERATURE_BELOW_NOMINAL = 16      	# 低温警告  
uint32 FLAG_TEMPERATURE_ABOVE_NOMINAL = 32      	# 高温警告  
uint32 FLAG_TEMPERATURE_OVERHEATING = 64      		# 严重过热  
uint32 FLAG_TEMPERATURE_EGT_ABOVE_NOMINAL = 128     	# 排气温度过高警告  

uint32 FLAG_FUEL_PRESSURE_SUPPORTED = 256		# 燃油压力。这些标志是可选的  
uint32 FLAG_FUEL_PRESSURE_BELOW_NOMINAL  = 512     	# 低压警告  
uint32 FLAG_FUEL_PRESSURE_ABOVE_NOMINAL = 1024   	# 高压警告  

uint32 FLAG_DETONATION_SUPPORTED = 2048			# 爆震警告。此标志是可选的。  
uint32 FLAG_DETONATION_OBSERVED = 4096    		# 观测到爆震条件警告  

uint32 FLAG_MISFIRE_SUPPORTED = 8192			# 失火警告。此标志是可选的。  
uint32 FLAG_MISFIRE_OBSERVED = 16384   			# 观测到失火条件警告  

uint32 FLAG_OIL_PRESSURE_SUPPORTED = 32768		# 机油压力。这些标志是可选的  
uint32 FLAG_OIL_PRESSURE_BELOW_NOMINAL = 65536   	# 低压警告  
uint32 FLAG_OIL_PRESSURE_ABOVE_NOMINAL = 131072  	# 高压警告  

uint32 FLAG_DEBRIS_SUPPORTED = 262144			# 碎片警告。此标志是可选的  
uint32 FLAG_DEBRIS_DETECTED = 524288  			# 碎片检测警告  
uint32 flags  

uint8 engine_load_percent				# 发动机负荷估计值，百分比，[0, 127]  
uint32 engine_speed_rpm					# 发动机转速，转每分钟  
float32 spark_dwell_time_ms 				# 点火保持时间，毫秒  
float32 atmospheric_pressure_kpa			# 大气（气压）压力，千帕  
float32 intake_manifold_pressure_kpa			# 进气歧管压力，千帕  
float32 intake_manifold_temperature			# 进气歧管温度，开尔文  
float32 coolant_temperature				# 冷却液温度，开尔文  
float32 oil_pressure					# 机油压力，千帕  
float32 oil_temperature					# 机油温度，开尔文  
float32 fuel_pressure					# 燃油压力，千帕  
float32 fuel_consumption_rate_cm3pm			# 实时燃油消耗估计值，（立方厘米）/分钟  
float32 estimated_consumed_fuel_volume_cm3		# 自发动机启动以来的燃油消耗估计值，立方厘米  
uint8 throttle_position_percent				# 节气门位置，百分比  
uint8 ecu_index						# 发布ECU的索引  

uint8 SPARK_PLUG_SINGLE         = 0  
uint8 SPARK_PLUG_FIRST_ACTIVE   = 1  
uint8 SPARK_PLUG_SECOND_ACTIVE  = 2  
uint8 SPARK_PLUG_BOTH_ACTIVE    = 3  
uint8 spark_plug_usage					# 火花塞活动报告。  

float32 ignition_timing_deg				# 气缸点火时机，曲轴角度度数  
float32 injection_time_ms				# 燃油喷射时间，毫秒  
float32 cylinder_head_temperature			# 气缸头温度（CHT），开尔文  
float32 exhaust_gas_temperature				# 排气温度（EGT），开尔文  
float32 lambda_coefficient				# 估计lambda系数，无量纲比值  
```