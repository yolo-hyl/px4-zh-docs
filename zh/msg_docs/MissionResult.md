# 任务结果 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MissionResult.msg)

```c
uint64 timestamp		# 系统启动后的时间（微秒）

uint32 mission_id   		# 生成结果的任务ID
uint32 geofence_id  		# 生成结果的地理围栏ID（用于任务可行性）
uint32 home_position_counter  	# 生成结果的家点位置计数器（用于任务可行性）

int32 seq_reached		# 已到达的任务项序列号（默认-1）
uint16 seq_current		# 当前任务项序列号
uint16 seq_total		# 任务项总数量

bool valid			# 任务是否有效
bool warning			# 任务是否有效但包含可能引发安全警告的问题项
bool finished			# 任务是否已完成
bool failure			# 任务是否因某种原因无法继续或完成

bool item_do_jump_changed	# 任务项剩余跳转次数是否已变化
uint16 item_changed_index	# 表示哪个任务项已变化
uint16 item_do_jump_remaining	# 设置该任务项的剩余跳转次数

uint8 execution_mode	# 表示任务执行模式

```