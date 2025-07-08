# 任务(UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Mission.msg)

```c
uint64 timestamp	# 系统启动后时间（微秒）
uint8 mission_dataman_id	# 默认0，dataman中有两个外部存储位置：0或1
uint8 fence_dataman_id		# 默认0，dataman中有两个外部存储位置：0或1
uint8 safepoint_dataman_id	# 默认0，dataman中有两个外部存储位置：0或1

uint16 count		# 存储在dataman中的任务数量
int32 current_seq	# 默认-1，从最新修改的任务开始

int32 land_start_index  # 降落起点标记的索引，若不可用则为降落任务项索引，否则为-1
int32 land_index 	# 降落任务项索引，否则为-1

uint32 mission_id # 表示任务更新，若发生变化则从dataman重新加载
uint32 geofence_id # 表示地理围栏更新，若发生变化则从dataman重新加载
uint32 safe_points_id # 表示安全点更新，若发生变化则从dataman重新加载

```