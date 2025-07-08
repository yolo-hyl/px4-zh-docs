# 应答器报告 (UORB 消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TransponderReport.msg)

```c
uint64 timestamp	# 系统启动以来的时间 (微秒)
uint32 icao_address 	# ICAO 地址
float64 lat 		# 纬度，以度表示
float64 lon 		# 经度，以度表示
uint8 altitude_type	# 来自 ADSB_ALTITUDE_TYPE 枚举的类型
float32 altitude 	# 海平面以上高度 (米)
float32 heading 	# 地面航向角 (弧度)，范围 -pi 至 +pi，0 表示正北
float32 hor_velocity	# 水平速度 (米/秒)
float32 ver_velocity 	# 垂直速度 (米/秒)，正数表示上升
char[9] callsign	# 8个字符的呼号+空字符
uint8 emitter_type 	# 来自 ADSB_EMITTER_TYPE 枚举的类型
uint8 tslc 		# 自上一次通信以来的秒数
uint16 flags 		# 用于指示各种状态的标志位，包括有效数据字段
uint16 squawk 		# 识别码
uint8[18] uas_id	# 唯一UAS标识符

# ADSB标志位
uint16 PX4_ADSB_FLAGS_VALID_COORDS = 1
uint16 PX4_ADSB_FLAGS_VALID_ALTITUDE = 2
uint16 PX4_ADSB_FLAGS_VALID_HEADING = 4
uint16 PX4_ADSB_FLAGS_VALID_VELOCITY = 8
uint16 PX4_ADSB_FLAGS_VALID_CALLSIGN = 16
uint16 PX4_ADSB_FLAGS_VALID_SQUAWK = 32
uint16 PX4_ADSB_FLAGS_RETRANSLATE = 256

#ADSB 发射机数据:
#from mavlink/v2.0/common/common.h
uint16 ADSB_EMITTER_TYPE_NO_INFO=0
uint16 ADSB_EMITTER_TYPE_LIGHT=1
uint16 ADSB_EMITTER_TYPE_SMALL=2
uint16 ADSB_EMITTER_TYPE_LARGE=3
uint16 ADSB_EMITTER_TYPE_HIGH_VORTEX_LARGE=4
uint16 ADSB_EMITTER_TYPE_HEAVY=5
uint16 ADSB_EMITTER_TYPE_HIGHLY_MANUV=6
uint16 ADSB_EMITTER_TYPE_ROTOCRAFT=7
uint16 ADSB_EMITTER_TYPE_UNASSIGNED=8
uint16 ADSB_EMITTER_TYPE_GLIDER=9
uint16 ADSB_EMITTER_TYPE_LIGHTER_AIR=10
uint16 ADSB_EMITTER_TYPE_PARACHUTE=11
uint16 ADSB_EMITTER_TYPE_ULTRA_LIGHT=12
uint16 ADSB_EMITTER_TYPE_UNASSIGNED2=13
uint16 ADSB_EMITTER_TYPE_UAV=14
uint16 ADSB_EMITTER_TYPE_SPACE=15
uint16 ADSB_EMITTER_TYPE_UNASSGINED3=16
uint16 ADSB_EMITTER_TYPE_EMERGENCY_SURFACE=17
uint16 ADSB_EMITTER_TYPE_SERVICE_SURFACE=18
uint16 ADSB_EMITTER_TYPE_POINT_OBSTACLE=19
uint16 ADSB_EMITTER_TYPE_ENUM_END=20

uint8 ORB_QUEUE_LENGTH = 16

```