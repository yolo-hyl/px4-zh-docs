# ModeCompleted (UORB message)

模式完成结果，由活动模式发布。  
nav_state 的可能值在 VehicleStatus 消息中定义。  
注意：此消息并非始终发布（例如用户切换模式或触发安全保护时）

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ModeCompleted.msg)

```c
# 模式完成结果，由活动模式发布。
# nav_state 的可能值在 VehicleStatus 消息中定义。
# 注意：此消息并非始终发布（例如用户切换模式或触发安全保护时）

uint32 MESSAGE_VERSION = 0

uint64 timestamp				# 系统启动以来的时间（微秒）


uint8 RESULT_SUCCESS = 0
# [1-99]: 保留
uint8 RESULT_FAILURE_OTHER = 100 # 模式失败（通用错误）

uint8 result                     # RESULT_* 中的任意值

uint8 nav_state                  # 源模式（VehicleStatus 中的值）
```