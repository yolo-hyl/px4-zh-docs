# RtlStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RtlStatus.msg)

```c
uint64 timestamp                      # 系统启动后经过的时间（微秒）

uint32 safe_points_id 		      # 当前安全点集合的唯一ID
bool is_evaluation_pending 	      # 标记RTL点是否需要重新评估（例如新安全点可用，但需要加载）。

bool has_vtol_approach 		      # 标记当前RTL_TYPE参数设置下是否定义了进近方式

uint8 rtl_type	      		      # 选择的RTL类型
uint8 safe_point_index 		      # 在RTL_STATUS_TYPE_DIRECT_SAFE_POINT模式下选择的安全点索引

uint8 RTL_STATUS_TYPE_NONE=0       		# 当前无法执行评估时挂起，例如仍在加载安全点时
uint8 RTL_STATUS_TYPE_DIRECT_SAFE_POINT=1 	# 直接前往安全点或家点位置
uint8 RTL_STATUS_TYPE_DIRECT_MISSION_LAND=2 	# 直接前往任务着陆起点
uint8 RTL_STATUS_TYPE_FOLLOW_MISSION=3 		# 从起始索引开始跟随任务航线至着陆。起始索引在任务模式下为当前航点，否则为最近航点
uint8 RTL_STATUS_TYPE_FOLLOW_MISSION_REVERSE=4 	# 从起始索引逆向跟随任务航线至任务起点。起始索引在任务模式下为上一航点，否则为最近航点
```