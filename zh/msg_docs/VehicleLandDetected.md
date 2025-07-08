# VehicleLandDetected (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleLandDetected.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp	# 系统启动后的时间 (微秒)

bool freefall		# 如果机体当前处于自由下落状态则为true
bool ground_contact	# 如果机体与地面接触但尚未着陆（1阶段）则为true
bool maybe_landed	# 如果机体可能已着陆（2阶段）则为true
bool landed		# 如果机体当前已着陆在地面（3阶段）则为true

bool in_ground_effect # 表示从着陆检测器的视角看机体可能处于地面效应中（气压计）。当机体水平方向无移动且处于下降状态时（粗略假设用户正在着陆），此标志将变为true
bool in_descend

bool has_low_throttle

bool vertical_movement
bool horizontal_movement
bool rotational_movement

bool close_to_ground_or_skipped_check

bool at_rest

```