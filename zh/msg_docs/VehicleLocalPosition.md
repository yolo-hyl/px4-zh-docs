# 机体局部位置 (UORB消息)

融合的局部位置（NED）。坐标系的原点是EKF2模块启动时的机体位置。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleLocalPosition.msg)

```c
# 融合的局部位置（NED）。
# 坐标系的原点是EKF2模块启动时的机体位置。

uint32 MESSAGE_VERSION = 0

uint64 timestamp			# 系统启动后时间（微秒）
uint64 timestamp_sample                 # 原始数据的时间戳（微秒）

bool xy_valid				# 当x和y有效时为true
bool z_valid				# 当z有效时为true
bool v_xy_valid				# 当vx和vy有效时为true
bool v_z_valid				# 当vz有效时为true

# 局部NED坐标系中的位置
float32 x				# NED地固坐标系中的北向位置（米）
float32 y				# NED地固坐标系中的东向位置（米）
float32 z				# NED地固坐标系中的下向位置（负高度）（米）

# 位置重置偏移量
float32[2] delta_xy			# 最新重置中位置估计的横向位移量（以x和y为单位）[m]
uint8 xy_reset_counter			# 最新横向位置估计重置的索引
float32 delta_z				# 最新重置中位置估计的垂直位移量[m]
uint8 z_reset_counter			# 最新垂直位置估计重置的索引

# NED坐标系中的速度
float32 vx 				# NED地固坐标系中的北向速度（米/秒）
float32 vy				# NED地固坐标系中的东向速度（米/秒）
float32 vz				# NED地固坐标系中的下向速度（米/秒）
float32 z_deriv				# NED地固坐标系中下向位置的时间导数（米/秒）

# 速度重置偏移量
float32[2] delta_vxy			# 最新重置中速度估计的横向位移量（以x和y为单位）[m/s]
uint8 vxy_reset_counter			# 最新横向速度估计重置的索引
float32 delta_vz			# 最新重置中速度估计的垂直位移量[m/s]
uint8 vz_reset_counter			# 最新垂直速度估计重置的索引

# NED坐标系中的加速度
float32 ax        # NED地固坐标系中北向速度导数（米/秒²）
float32 ay        # NED地固坐标系中东向速度导数（米/秒²）
float32 az        # NED地固坐标系中下向速度导数（米/秒²）

float32 heading				# 将切线平面相对于NED地固坐标系转换的欧拉偏航角，-PI..+PI，（弧度）
float32 heading_var
float32 unaided_heading                 # 与heading相同，但仅通过积分校正后的陀螺仪数据生成
float32 delta_heading			# 最新偏航重置引起的偏航角差值[rad]
uint8 heading_reset_counter		# 最新偏航重置的索引
bool heading_good_for_control

float32 tilt_var

# 参考点（局部NED坐标系原点）在全局（GPS/WGS84）坐标系中的位置
bool xy_global				# 当(x,y)具有有效全局参考（ref_lat, ref_lon）时为true
bool z_global				# 当z具有有效全局参考（ref_alt）时为true
uint64 ref_timestamp			# 设置参考位置时的系统启动时间（微秒）
float64 ref_lat				# 参考点纬度（度）
float64 ref_lon				# 参考点经度（度）
float32 ref_alt				# 参考海拔（相对于海平面）（米）

# 地面距离
bool dist_bottom_valid			# 当到底部表面的距离有效时为true
float32 dist_bottom			# 从底部表面到地面的距离（米）
float32 dist_bottom_var                 # 地形估计方差（m²）

float32 delta_dist_bottom               # 最新重置中距离底部估计的垂直位移量[m]
uint8 dist_bottom_reset_counter         # 最新距离底部估计重置的索引

uint8 dist_bottom_sensor_bitfield	# 位字段指示用于估计dist_bottom的传感器类型
uint8 DIST_BOTTOM_SENSOR_NONE = 0
uint8 DIST_BOTTOM_SENSOR_RANGE = 1	# (1 << 0) 使用测距传感器估计dist_bottom字段
uint8 DIST_BOTTOM_SENSOR_FLOW = 2	# (1 << 1) 使用流传感器估计dist_bottom字段（主要用于固定翼）

float32 eph				# 水平位置误差的标准差（米）
float32 epv				# 垂直位置误差的标准差（米）
float32 evh				# 水平速度误差的标准差（米/秒）
float32 evv				# 垂直速度误差的标准差（米/秒）

bool dead_reckoning                     # 如果此位置通过航迹推算估计则为true

# 估计器指定的机体限制
# 当不需要限制时设置为INFINITY
float32 vxy_max				# 最大水平速度（米/秒）
float32 vz_max				# 最大垂直速度（米/秒）
float32 hagl_min			# 最小离地高度（米）
float32 hagl_max_z			# z控制的最大离地高度（米）
float32 hagl_max_xy			# xy控制的最大离地高度（米）

# TOPICS vehicle_local_position vehicle_local_position_groundtruth external_ins_local_position
# TOPICS estimator_local_position

```