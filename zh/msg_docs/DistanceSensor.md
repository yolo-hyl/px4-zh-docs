# 距离传感器 (UORB消息)

DISTANCE_SENSOR 消息数据

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DistanceSensor.msg)

```c
# DISTANCE_SENSOR 消息数据

uint64 timestamp		# 系统启动后的时间 (微秒)

uint32 device_id		# 传感器的唯一设备ID，断电重启后不变

float32 min_distance		# 传感器可测量的最小距离 (米)
float32 max_distance		# 传感器可测量的最大距离 (米)
float32 current_distance	# 当前距离读数 (米)
float32 variance		# 测量方差 (米²)，0 表示未知/无效读数
int8 signal_quality		# 信号质量百分比 (0...100%)，0=无效信号，100=完美信号，-1=未知信号质量

uint8 type			# 来自 MAV_DISTANCE_SENSOR 枚举的类型
uint8 MAV_DISTANCE_SENSOR_LASER = 0
uint8 MAV_DISTANCE_SENSOR_ULTRASOUND = 1
uint8 MAV_DISTANCE_SENSOR_INFRARED = 2
uint8 MAV_DISTANCE_SENSOR_RADAR = 3

float32 h_fov # 传感器水平视场角 (弧度)
float32 v_fov # 传感器垂直视场角 (弧度)
float32[4] q # 传感器相对于机体的四元数方向，用于指定 ROTATION_CUSTOM 的方向

uint8 orientation		# 传感器方向来自 MAV_SENSOR_ORIENTATION 枚举

uint8 ROTATION_YAW_0		= 0 # MAV_SENSOR_ROTATION_NONE
uint8 ROTATION_YAW_45		= 1 # MAV_SENSOR_ROTATION_YAW_45
uint8 ROTATION_YAW_90		= 2 # MAV_SENSOR_ROTATION_YAW_90
uint8 ROTATION_YAW_135		= 3 # MAV_SENSOR_ROTATION_YAW_135
uint8 ROTATION_YAW_180		= 4 # MAV_SENSOR_ROTATION_YAW_180
uint8 ROTATION_YAW_225		= 5 # MAV_SENSOR_ROTATION_YAW_225
uint8 ROTATION_YAW_270		= 6 # MAV_SENSOR_ROTATION_YAW_270
uint8 ROTATION_YAW_315		= 7 # MAV_SENSOR_ROTATION_YAW_315

uint8 ROTATION_FORWARD_FACING	= 0 # MAV_SENSOR_ROTATION_NONE
uint8 ROTATION_RIGHT_FACING	= 2 # MAV_SENSOR_ROTATION_YAW_90
uint8 ROTATION_BACKWARD_FACING	= 4 # MAV_SENSOR_ROTATION_YAW_180
uint8 ROTATION_LEFT_FACING	= 6 # MAV_SENSOR_ROTATION_YAW_270

uint8 ROTATION_UPWARD_FACING   = 24 # MAV_SENSOR_ROTATION_PITCH_90
uint8 ROTATION_DOWNWARD_FACING = 25 # MAV_SENSOR_ROTATION_PITCH_270

uint8 ROTATION_CUSTOM          = 100 # MAV_SENSOR_ROTATION_CUSTOM

uint8 mode
uint8 MODE_UNKNOWN  = 0
uint8 MODE_ENABLED  = 1
uint8 MODE_DISABLED = 2
```