# 执行器测试 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorTest.msg)

```c
uint64 timestamp				# 系统启动后经过的时间（微秒）

# 用于测试单个执行器输出功能的主题

uint8 ACTION_RELEASE_CONTROL = 0	# 退出指定功能的测试模式
uint8 ACTION_DO_CONTROL = 1			# 启用执行器测试模式

uint8 FUNCTION_MOTOR1 = 101
uint8 MAX_NUM_MOTORS  = 12
uint8 FUNCTION_SERVO1 = 201
uint8 MAX_NUM_SERVOS  = 8

uint8 action					# 对应ACTION_*中的一种
uint16 function					# 执行器输出功能
float32 value					# 范围：[-1, 1]，其中1表示最大正向输出，
								# 0表示舵机中位或电机最小推力，
                   				# -1表示最大负向输出（若电机不支持负值则映射为NaN），
                   				# NaN表示解除武装（停止电机）
uint32 timeout_ms				# 超时时间（毫秒），超时后退出测试模式（若为0则不会超时）

uint8 ORB_QUEUE_LENGTH = 16                     # >= MAX_NUM_MOTORS 以支持esc_calibration中的代码
```