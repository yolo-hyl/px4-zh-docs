# 地理围栏结果 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GeofenceResult.msg)

```c
uint64 timestamp                        # 系统启动后经过的时间（微秒）
uint8 GF_ACTION_NONE = 0                # 地理围栏违规时不采取任何操作
uint8 GF_ACTION_WARN = 1                # 发送关键mavlink消息
uint8 GF_ACTION_LOITER = 2              # 切换至AUTO|LOITER模式
uint8 GF_ACTION_RTL = 3                 # 切换至AUTO|RTL模式
uint8 GF_ACTION_TERMINATE = 4           # 飞行终止
uint8 GF_ACTION_LAND = 5                # 切换至AUTO|LAND模式

bool geofence_max_dist_triggered	# 当触发最大距离检查时为true
bool geofence_max_alt_triggered		# 当触发最大高度检查时为true
bool geofence_custom_fence_triggered	# 当触发自定义包含/排除地理围栏检查时为true

uint8 geofence_action           	# 地理围栏违规时采取的操作
```