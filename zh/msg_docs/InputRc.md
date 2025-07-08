# InputRc (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/InputRc.msg)

```c
uint64 timestamp			# 系统启动后的时间（微秒）

uint8 RC_INPUT_SOURCE_UNKNOWN = 0
uint8 RC_INPUT_SOURCE_PX4FMU_PPM = 1
uint8 RC_INPUT_SOURCE_PX4IO_PPM = 2
uint8 RC_INPUT_SOURCE_PX4IO_SPEKTRUM = 3
uint8 RC_INPUT_SOURCE_PX4IO_SBUS = 4
uint8 RC_INPUT_SOURCE_PX4IO_ST24 = 5
uint8 RC_INPUT_SOURCE_MAVLINK = 6
uint8 RC_INPUT_SOURCE_QURT = 7
uint8 RC_INPUT_SOURCE_PX4FMU_SPEKTRUM = 8
uint8 RC_INPUT_SOURCE_PX4FMU_SBUS = 9
uint8 RC_INPUT_SOURCE_PX4FMU_ST24 = 10
uint8 RC_INPUT_SOURCE_PX4FMU_SUMD = 11
uint8 RC_INPUT_SOURCE_PX4FMU_DSM = 12
uint8 RC_INPUT_SOURCE_PX4IO_SUMD = 13
uint8 RC_INPUT_SOURCE_PX4FMU_CRSF = 14
uint8 RC_INPUT_SOURCE_PX4FMU_GHST = 15

uint8 RC_INPUT_MAX_CHANNELS = 18 	# 系统中最大遥控输入通道数。S.Bus 最多支持 18 个通道。

uint64 timestamp_last_signal		# 最后一次有效接收时间

uint8 channel_count			# 实际检测到的通道数

int8 RSSI_MAX = 100
int32 rssi				# 接收信号强度指示（RSSI）：< 0：未定义，0：无信号，100：全接收

bool rc_failsafe			# 显式故障保护标志：发射器故障或超出范围时为true，其他情况为false。只有true状态可靠，因为市面上部分（PPM）接收机在没有明确通知的情况下也会进入故障保护状态。
bool rc_lost				# 遥控接收机连接状态：如果在预期时间内未收到帧则为true，其他情况为false。true通常表示接收机已断开连接，也可能表示"非智能"系统中的无线电链路丢失。如果接收机在链路丢失后仍继续传输帧（具有故障保护功能的接收机），将保持为false。

uint16 rc_lost_frame_count		# 丢失的遥控帧数。注意：预期用途是：当RSSI不可用时观察无线电链路质量。此值不得用于触发任何类似故障保护的功能。
uint16 rc_total_frame_count		# 总遥控帧数。注意：预期用途是：当RSSI不可用时观察无线电链路质量。此值不得用于触发任何类似故障保护的功能。
uint16 rc_ppm_frame_length		# 单个PPM帧的长度。非PPM系统为0

uint8 input_source			# 输入源
uint16[18] values			# 每个支持通道的测量脉宽

int8 link_quality			# 链路质量。百分比0-100%。-1 = 无效
float32 rssi_dbm			# 实际RSSI值，单位dBm。NaN = 无效
```