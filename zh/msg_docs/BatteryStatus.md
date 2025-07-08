# BatteryStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/BatteryStatus.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp				# 系统启动后的时间(微秒)
bool connected					# 根据电压阈值判断电池是否连接
float32 voltage_v				# 电池电压(伏特)，未知时为0
float32 current_a				# 电池电流(安培)，未知时为-1
float32 current_average_a			# 平均电池电流(安培)(固定翼平飞平均值)，未知时为-1
float32 discharged_mah				# 放电容量(mAh)，未知时为-1
float32 remaining				# 从1到0的剩余电量，未知时为-1
float32 scale					# 功率缩放系数，>=1 或未知时为-1
float32 time_remaining_s			# 预测在当前平均负载下电池耗尽的剩余时间(秒)，未知时为NAN
float32 temperature				# 电池温度(摄氏度)，未知时为NaN
uint8 cell_count				# 电池单元数量，未知时为0

uint8 SOURCE_POWER_MODULE = 0
uint8 SOURCE_EXTERNAL = 1
uint8 SOURCE_ESCS = 2
uint8 source					# 电池来源
uint8 priority					# 电源控制器V1..Vn的连接优先级(零基)
uint16 capacity					# 电池实际容量
uint16 cycle_count				# 电池经历的放电循环次数
uint16 average_time_to_empty			# 基于平均放电率的剩余电池容量预测(分钟)
uint16 serial_number				# 电池组序列号
uint16 manufacture_date				# 制造日期，电池组序列号的一部分。格式：Day + Month×32 + (Year–1980)×512
uint16 state_of_health				# 健康状态。满电容量/设计容量，0-100%。
uint16 max_error				# 最大误差，预期的荷电状态计算误差百分比，范围1-100%
uint8 id					# 电池ID号。应为机体唯一且一致的编号。1开始计数。
uint16 interface_error				# 接口错误计数器

float32[14] voltage_cell_v			# 单个电池单元电压，未知时为0
float32 max_cell_voltage_delta			# 单个电池单元电压的最大差异

bool is_powering_off				# 电源关闭事件即将发生指示，未知时为false
bool is_required				# 在上电前是否需要该电池

uint8 WARNING_NONE = 0			# 无电池低电压警告
uint8 WARNING_LOW = 1			# 低电压警告
uint8 WARNING_CRITICAL = 2		# 关键电压，立即返航/中止
uint8 WARNING_EMERGENCY = 3		# 需立即降落
uint8 WARNING_FAILED = 4		# 电池完全故障
uint8 STATE_UNHEALTHY = 6 		# 电池检测到缺陷或错误，不建议/禁止使用。可能原因(故障)列于故障字段。
uint8 STATE_CHARGING = 7 		# 电池正在充电

uint8 FAULT_DEEP_DISCHARGE = 0 		# 电池深度放电
uint8 FAULT_SPIKES = 1 			# 电压尖峰
uint8 FAULT_CELL_FAIL= 2 		# 一个或多个电池单元故障
uint8 FAULT_OVER_CURRENT = 3 		# 过电流
uint8 FAULT_OVER_TEMPERATURE = 4 	# 过温
uint8 FAULT_UNDER_TEMPERATURE = 5 	# 低温故障
uint8 FAULT_INCOMPATIBLE_VOLTAGE = 6 	# 机体电压与该电池不兼容(同一路电源的电池电压应相似)
uint8 FAULT_INCOMPATIBLE_FIRMWARE = 7 	# 电池固件与当前飞控固件不兼容
uint8 FAULT_INCOMPATIBLE_MODEL = 8 	# 电池型号不被系统支持
uint8 FAULT_HARDWARE_FAILURE = 9 	# 硬件问题
uint8 FAULT_FAILED_TO_ARM = 10 		# 上电时发生电池故障
uint8 FAULT_COUNT = 11 			# 计数器 - 保留为最后一个元素！

uint16 faults					# 智能电池电源状态/故障标志(位掩码)，用于健康状态指示。
uint8 warning					# 当前电池警告

uint8 MAX_INSTANCES = 4

float32 full_charge_capacity_wh     		# 补偿后的电池容量
float32 remaining_capacity_wh       		# 补偿后的剩余电池容量
uint16 over_discharge_count         		# 电池过放电次数
float32 nominal_voltage             		# 电池组标称电压

float32 internal_resistance_estimate            # [Ohm] 每个电池单元的内阻估计值
float32 ocv_estimate                            # [V] 开路电压估计值
float32 ocv_estimate_filtered			# [V] 滤波后的开路电压估计值
float32 volt_based_soc_estimate			# [0, 1] 基于电压的标准化荷电状态估计值
float32 voltage_prediction			# [V] 电压预测值
float32 prediction_error                        # [V] 预测误差
float32 estimation_covariance_norm		# 协方差矩阵的范数
```