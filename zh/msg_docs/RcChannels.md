# RcChannels (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RcChannels.msg)

```c
uint64 timestamp						# 系统启动后的时间（微秒）

uint8 FUNCTION_THROTTLE      = 0
uint8 FUNCTION_ROLL          = 1
uint8 FUNCTION_PITCH         = 2
uint8 FUNCTION_YAW           = 3
uint8 FUNCTION_RETURN        = 4
uint8 FUNCTION_LOITER        = 5
uint8 FUNCTION_OFFBOARD      = 6
uint8 FUNCTION_FLAPS         = 7
uint8 FUNCTION_AUX_1         = 8
uint8 FUNCTION_AUX_2         = 9
uint8 FUNCTION_AUX_3         = 10
uint8 FUNCTION_AUX_4         = 11
uint8 FUNCTION_AUX_5         = 12
uint8 FUNCTION_AUX_6         = 13
uint8 FUNCTION_PARAM_1       = 14
uint8 FUNCTION_PARAM_2       = 15
uint8 FUNCTION_PARAM_3_5     = 16
uint8 FUNCTION_KILLSWITCH    = 17
uint8 FUNCTION_TRANSITION    = 18
uint8 FUNCTION_GEAR          = 19
uint8 FUNCTION_ARMSWITCH     = 20
uint8 FUNCTION_FLTBTN_SLOT_1 = 21
uint8 FUNCTION_FLTBTN_SLOT_2 = 22
uint8 FUNCTION_FLTBTN_SLOT_3 = 23
uint8 FUNCTION_FLTBTN_SLOT_4 = 24
uint8 FUNCTION_FLTBTN_SLOT_5 = 25
uint8 FUNCTION_FLTBTN_SLOT_6 = 26
uint8 FUNCTION_ENGAGE_MAIN_MOTOR = 27
uint8 FUNCTION_PAYLOAD_POWER = 28

uint8 FUNCTION_FLTBTN_SLOT_COUNT = 6

uint64 timestamp_last_valid					# 最近一次有效遥控信号的时间戳
float32[18] channels						# 缩放至-1..1（油门：0..1）
uint8 channel_count						# 有效通道数量
int8[29] function						# 功能映射
uint8 rssi							# 接收信号强度指数
bool signal_lost						# 控制信号丢失，应与话题超时配合检查
uint32 frame_drop_count						# 丢帧数量

```