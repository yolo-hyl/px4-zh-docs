# ModeCompleted (UORB message)

模式完成结果，由活动模式发布。  
nav_state 的可能值在 VehicleStatus 消息中定义。  
注意：此消息并非始终发布（例如用户切换模式或触发安全保护时）

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ModeCompleted.msg)

```c
# Mode completion result, published by an active mode.
# The possible values of nav_state are defined in the VehicleStatus msg.
# Note that this is not always published (e.g. when a user switches modes or on
# failsafe activation)

uint32 MESSAGE_VERSION = 0

uint64 timestamp				 # time since system start (microseconds)


uint8 RESULT_SUCCESS = 0
# [1-99]: reserved
uint8 RESULT_FAILURE_OTHER = 100 # Mode failed (generic error)

uint8 result                     # One of RESULT_*

uint8 nav_state                  # Source mode (values in VehicleStatus)

```