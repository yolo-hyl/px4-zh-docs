# NavigatorStatus（UORB消息）

Navigator模式的当前状态。nav_state的可能值定义在VehicleStatus消息中。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/NavigatorStatus.msg)

```c
# Navigator模式的当前状态
# nav_state的可能值定义在VehicleStatus消息中
uint64 timestamp  # 自系统启动以来的时间（微秒）

uint8 nav_state   # 源模式（VehicleStatus中的值）
uint8 failure     # Navigator故障枚举

uint8 FAILURE_NONE = 0
uint8 FAILURE_HAGL = 1 # 目标高度超过最大高度
```