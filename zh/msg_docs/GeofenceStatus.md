# 地理围栏状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GeofenceStatus.msg)

```c
uint64 timestamp                        # 系统启动后时间(微秒)

uint32 geofence_id 			# 已加载的地理围栏ID
uint8 status 				# 当前地理围栏状态

uint8 GF_STATUS_LOADING = 0
uint8 GF_STATUS_READY = 1
```