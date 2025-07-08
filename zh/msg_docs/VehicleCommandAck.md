# VehicleCommandAck（UORB消息）

Vehicle Command Ackonwledgement uORB消息。
用于确认接收的机体命令。
遵循MAVLink COMMAND_ACK消息定义

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleCommandAck.msg)

```c
# Vehicle Command Ackonwledgement uORB消息。
# 用于确认接收的机体命令。
# 遵循MAVLink COMMAND_ACK消息定义

uint32 MESSAGE_VERSION = 0

uint64 timestamp		# 系统启动后时间戳（微秒）

# 结果状态。遵循MAVLink MAV_RESULT枚举定义
uint8 VEHICLE_CMD_RESULT_ACCEPTED = 0			# 命令已接受并执行 |
uint8 VEHICLE_CMD_RESULT_TEMPORARILY_REJECTED = 1	# 命令临时拒绝/否定 |
uint8 VEHICLE_CMD_RESULT_DENIED = 2			# 命令永久否定 |
uint8 VEHICLE_CMD_RESULT_UNSUPPORTED = 3		# 命令未知/不支持 |
uint8 VEHICLE_CMD_RESULT_FAILED = 4			# 命令执行失败 |
uint8 VEHICLE_CMD_RESULT_IN_PROGRESS = 5		# 命令正在执行 |
uint8 VEHICLE_CMD_RESULT_CANCELLED = 6			# 命令已取消

# 授权拒绝的具体情况
uint16 ARM_AUTH_DENIED_REASON_GENERIC = 0
uint16 ARM_AUTH_DENIED_REASON_NONE = 1
uint16 ARM_AUTH_DENIED_REASON_INVALID_WAYPOINT = 2
uint16 ARM_AUTH_DENIED_REASON_TIMEOUT = 3
uint16 ARM_AUTH_DENIED_REASON_AIRSPACE_IN_USE = 4
uint16 ARM_AUTH_DENIED_REASON_BAD_WEATHER = 5

uint8 ORB_QUEUE_LENGTH = 4

uint32 command						# 正在确认的命令
uint8 result						# 命令结果
uint8 result_param1					# 也用作进度[%]，可设置命令被拒绝的原因，或当结果为MAV_RESULT_IN_PROGRESS时的进度百分比
int32 result_param2					# 结果附加参数，例如：哪个参数导致MAV_CMD_NAV_WAYPOINT被拒绝。
uint8 target_system
uint16 target_component 				# 目标组件/模式执行器

bool from_external					# 表示命令是否来自外部源

```