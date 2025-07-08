# 相机状态（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CameraStatus.msg)

```c
uint64 时间戳		# 系统启动后经过的时间（微秒）

uint8 活动系统ID		# 当前活动相机的mavlink系统ID
uint8 活动组件ID 	# 当前活动相机的mavlink组件ID
```