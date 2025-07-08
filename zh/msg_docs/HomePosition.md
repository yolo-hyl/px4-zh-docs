# HomePosition (UORB message)

GPS家位置的WGS84坐标

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/HomePosition.msg)

```c
# GPS家位置的WGS84坐标

uint32 MESSAGE_VERSION = 0

uint64 timestamp			# 系统启动后经过的时间（微秒）

float64 lat				# 纬度（度）
float64 lon				# 经度（度）
float32 alt				# 海拔高度（米）

float32 x				# X坐标（米）
float32 y				# Y坐标（米）
float32 z				# Z坐标（米）

float32 yaw				# 偏航角（弧度）

bool valid_alt		# 高度已被设置时为true
bool valid_hpos		# 纬度和经度已被设置时为true
bool valid_lpos		# 本地坐标(xyz)已被设置时为true

bool manual_home	# 家位置被手动设置时为true

uint32 update_count 	# 家位置的更新计数器
```