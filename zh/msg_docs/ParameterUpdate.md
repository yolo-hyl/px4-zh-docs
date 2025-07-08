# 参数更新（UORB消息）

该消息用于通知系统一个或多个参数发生了变化

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ParameterUpdate.msg)

```c
# 该消息用于通知系统一个或多个参数发生了变化

uint64 timestamp		# 系统启动后的时间（微秒）

uint32 instance		# 实例计数 - 持续递增

uint32 get_count
uint32 set_count
uint32 find_count
uint32 export_count

uint16 active
uint16 changed
uint16 custom_default

```