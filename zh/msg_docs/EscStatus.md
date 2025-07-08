# EscStatus (UORB 消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/EscStatus.msg)

```c
uint64 timestamp					# 系统启动后的时间（微秒）
uint8 CONNECTED_ESC_MAX = 8				# 系统支持的电调数量。当前（2013年Q2）支持8个电调

uint8 ESC_CONNECTION_TYPE_PPM = 0			# 传统 PPM 电调
uint8 ESC_CONNECTION_TYPE_SERIAL = 1			# 串行总线连接的电调
uint8 ESC_CONNECTION_TYPE_ONESHOT = 2			# 单次 PPM
uint8 ESC_CONNECTION_TYPE_I2C = 3			# I2C
uint8 ESC_CONNECTION_TYPE_CAN = 4			# CAN-Bus
uint8 ESC_CONNECTION_TYPE_DSHOT = 5			# DShot

uint16 counter  					# 每次写入新数据时由写入线程递增

uint8 esc_count						# 已连接电调数量
uint8 esc_connectiontype				# 电调与系统连接方式

uint8 esc_online_flags					# 位掩码指示哪个电调在线/离线
# esc_online_flags 第0位 : 为1表示电调0在线
# esc_online_flags 第1位 : 为1表示电调1在线
# esc_online_flags 第2位 : 为1表示电调2在线
# esc_online_flags 第3位 : 为1表示电调3在线
# esc_online_flags 第4位 : 为1表示电调4在线
# esc_online_flags 第5位 : 为1表示电调5在线
# esc_online_flags 第6位 : 为1表示电调6在线
# esc_online_flags 第7位 : 为1表示电调7在线

uint8 esc_armed_flags					# 位掩码指示哪个电调处于上锁状态。对于电调本身未返回上锁状态的情况，上锁位应该始终设置

EscReport[8] esc

```