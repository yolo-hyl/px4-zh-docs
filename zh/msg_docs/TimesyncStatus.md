# TimesyncStatus（UORB消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TimesyncStatus.msg)

```c
uint64 timestamp			# 系统启动以来的时间（微秒）

uint8 SOURCE_PROTOCOL_UNKNOWN = 0
uint8 SOURCE_PROTOCOL_MAVLINK = 1
uint8 SOURCE_PROTOCOL_DDS     = 2
uint8 source_protocol			# 时间同步源

uint64 remote_timestamp			# 远端系统时间戳（微秒）
int64 observed_offset			# 从该时间同步包直接观测到的原始时间偏移（微秒）
int64 estimated_offset			# 伴飞系统与PX4之间的平滑时间偏移（微秒）
uint32 round_trip_time			# 该时间同步包的往返时间（微秒）

```