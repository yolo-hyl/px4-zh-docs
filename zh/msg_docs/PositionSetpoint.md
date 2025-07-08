# 位置设定点（UORB消息）

此文件仅作为依赖项在position_setpoint三元组中使用

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PositionSetpoint.msg)

```c
# 此文件仅作为依赖项在position_setpoint三元组中使用

uint64 timestamp		# 系统启动后时间（微秒）

uint8 SETPOINT_TYPE_POSITION=0	# 位置设定点
uint8 SETPOINT_TYPE_VELOCITY=1	# 速度设定点
uint8 SETPOINT_TYPE_LOITER=2	# 盘旋设定点
uint8 SETPOINT_TYPE_TAKEOFF=3	# 起飞设定点
uint8 SETPOINT_TYPE_LAND=4	# 降落设定点，必须忽略高度，持续下降直至着陆
uint8 SETPOINT_TYPE_IDLE=5	# 无操作，关闭电机或保持怠速（多旋翼）

uint8 LOITER_TYPE_ORBIT=0 	# 圆形轨迹
uint8 LOITER_TYPE_FIGUREEIGHT=1 # 类8字形轨迹

bool valid			# 设定点是否有效
uint8 type			# 用于调整位置控制器行为的设定点类型

float32 vx			# NED坐标系下本地速度设定点（m/s）
float32 vy			# NED坐标系下本地速度设定点（m/s）
float32 vz			# NED坐标系下本地速度设定点（m/s）

float64 lat			# 纬度（度）
float64 lon			# 经度（度）
float32 alt			# 海拔高度（米）
float32 yaw			# 偏航角（仅悬停时有效），弧度制（-PI..PI），NaN = 由飞行任务控制

float32 loiter_radius		# 盘旋主轴半径（米）
float32 loiter_minor_radius	# 盘旋次轴半径（用于非圆形轨迹）（米）
bool loiter_direction_counter_clockwise # 默认顺时针盘旋，通过此字段可修改方向
float32 loiter_orientation 	# 主轴相对于真北的方向（弧度制 -pi,pi）
uint8 	loiter_pattern		# 需遵循的盘旋模式

float32 acceptance_radius   # 水平接受半径（米）
float32 alt_acceptance_radius # 垂直接受半径，仅固定翼引导使用，NAN = 由引导模块选择（米）

float32 cruising_speed		# 常规巡航速度（非硬性约束）
bool gliding_enabled		# 如果机体具备能力则命令其滑翔（仅固定翼）
float32 cruising_throttle	# 常规巡航油门（非硬性约束），仅对地面车辆有效

```