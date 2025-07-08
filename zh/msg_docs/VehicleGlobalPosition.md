# 机体全局位置 (UORB消息)

融合的WGS84全局位置。
该结构体包含全局位置估计。这不是原始的GPS测量数据（@see vehicle_gps_position）。此话题通常由位置估计器发布，该估计器在计算时会综合考虑比GPS更多的信息源，例如在卡尔曼滤波实现中机体的控制输入。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleGlobalPosition.msg)

```c
# 融合的WGS84全局位置。
# 该结构体包含全局位置估计。这不是原始的GPS测量数据
# （@see vehicle_gps_position）。此话题通常由位置估计器发布，
# 该估计器在计算时会综合考虑比GPS更多的信息源，
# 例如在卡尔曼滤波实现中机体的控制输入。
#

uint32 MESSAGE_VERSION = 0

uint64 timestamp		# 系统启动后经过的时间 (微秒)
uint64 timestamp_sample         # 原始数据的采集时间戳 (微秒)

float64 lat			# 纬度 (度)
float64 lon			# 经度 (度)
float32 alt			# 海拔高度 (米)
float32 alt_ellipsoid		# 椭球面以上高度 (米)

bool lat_lon_valid
bool alt_valid

float32 delta_alt 	# 高度重置增量
float32 delta_terrain   # 地形重置增量
uint8 lat_lon_reset_counter	# 水平位置坐标重置事件计数器
uint8 alt_reset_counter 	# 高度重置事件计数器
uint8 terrain_reset_counter     # 地形重置事件计数器

float32 eph			# 水平位置误差的标准差 (米)
float32 epv			# 垂直位置误差的标准差 (米)

float32 terrain_alt		# WGS84地形高度 (米)
bool terrain_alt_valid		# 地形高度估计是否有效

bool dead_reckoning		# 如果该位置是通过推算估计的则为真

# TOPICS vehicle_global_position vehicle_global_position_groundtruth external_ins_global_position
# TOPICS estimator_global_position
# TOPICS aux_global_position

```