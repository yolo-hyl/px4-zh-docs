# UavcanParameterRequest (UORB message)

UAVCAN-MAVLink 参数桥接请求类型

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/UavcanParameterRequest.msg)

```c
# UAVCAN-MAVLink 参数桥接请求类型
uint64 timestamp		# 自系统启动以来的时间（微秒）

uint8 MESSAGE_TYPE_PARAM_REQUEST_READ = 20	# MAVLINK_MSG_ID_PARAM_REQUEST_READ
uint8 MESSAGE_TYPE_PARAM_REQUEST_LIST = 21	# MAVLINK_MSG_ID_PARAM_REQUEST_LIST
uint8 MESSAGE_TYPE_PARAM_SET = 23		# MAVLINK_MSG_ID_PARAM_SET
uint8 message_type				# MAVLink 消息类型: PARAM_REQUEST_READ, PARAM_REQUEST_LIST, PARAM_SET

uint8 NODE_ID_ALL = 0		# MAV_COMP_ID_ALL
uint8 node_id			# 从 MAVLink 组件 ID 映射的 UAVCAN 节点 ID

char[17] param_id		# MAVLink/UAVCAN 参数名称
int16 param_index		# 如果 param_id 字段用作标识符则为 -1

uint8 PARAM_TYPE_UINT8 = 1	# MAV_PARAM_TYPE_UINT8
uint8 PARAM_TYPE_INT64 = 8	# MAV_PARAM_TYPE_INT64
uint8 PARAM_TYPE_REAL32 = 9	# MAV_PARAM_TYPE_REAL32
uint8 param_type		# MAVLink 参数类型

int64 int_value			# 当 param_type 为整型时的当前值
float32 real_value		# 当 param_type 为浮点型时的当前值

uint8 ORB_QUEUE_LENGTH = 4

```