# PositionControllerLandingStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PositionControllerLandingStatus.msg)

```c
uint64 timestamp # [us] 系统启动后的时间
float32 lateral_touchdown_offset # [m] 着陆期间手动指令的横向着陆位置偏移量
bool flaring # 当机体正在拉平时为true

# 中止状态为：
# 0 表示未中止
# >0 表示已中止，具体中止原因由以下中止原因列表对应
uint8 abort_status

# 中止原因
# 在手动操作员中止后，对应参数FW_LND_ABORT的各个位
uint8 NOT_ABORTED = 0
uint8 ABORTED_BY_OPERATOR = 1
uint8 TERRAIN_NOT_FOUND = 2 # FW_LND_ABORT (1 << 0)
uint8 TERRAIN_TIMEOUT = 3 # FW_LND_ABORT (1 << 1)
uint8 UNKNOWN_ABORT_CRITERION = 4
```