# EscReport (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EscReport.msg)

```c
uint64 timestamp					# 系统启动后经过的时间（微秒）
uint32 esc_errorcount					# 电调报告的错误次数（如果支持）
int32 esc_rpm						# 电机转速（负值表示反向旋转）[RPM]（如果支持）
float32 esc_voltage					# 当前电调测量电压 [V]（如果支持）
float32 esc_current					# 当前电调测量电流 [A]（如果支持）
float32 esc_temperature					# 当前电调测量温度 [degC]（如果支持）
uint8 esc_address					# 当前电调地址（通常为1-8/必须由驱动设置）
uint8 esc_cmdcount					# 命令计数器

uint8 esc_state					# 电调状态（取决于厂商）

uint8 actuator_function				# 执行器输出功能（Motor1...MotorN之一）

uint16 failures					# 位掩码表示电调内部故障
int8 esc_power					# 应用功率 0-100%（负值保留）

uint8 FAILURE_OVER_CURRENT = 0 			# (1 << 0)
uint8 FAILURE_OVER_VOLTAGE = 1 			# (1 << 1)
uint8 FAILURE_MOTOR_OVER_TEMPERATURE = 2 	# (1 << 2)
uint8 FAILURE_OVER_RPM = 3			# (1 << 3)
uint8 FAILURE_INCONSISTENT_CMD = 4 		# (1 << 4)  当电调接收到不一致命令时设置（即超出范围）
uint8 FAILURE_MOTOR_STUCK = 5			# (1 << 5)
uint8 FAILURE_GENERIC = 6			# (1 << 6)
uint8 FAILURE_MOTOR_WARN_TEMPERATURE = 7	# (1 << 7)
uint8 FAILURE_WARN_ESC_TEMPERATURE = 8		# (1 << 8)
uint8 FAILURE_OVER_ESC_TEMPERATURE = 9		# (1 << 9)
uint8 ESC_FAILURE_COUNT = 10 			# 计数器 - 保留为最后一个元素！
```