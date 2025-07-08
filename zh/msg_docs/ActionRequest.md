# ActionRequest（UORB消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActionRequest.msg)  

```c
uint64 timestamp # 系统启动后经过的时间（微秒）

uint8 action # 请求了什么动作
uint8 ACTION_DISARM = 0
uint8 ACTION_ARM = 1
uint8 ACTION_TOGGLE_ARMING = 2
uint8 ACTION_UNKILL = 3
uint8 ACTION_KILL = 4
uint8 ACTION_SWITCH_MODE = 5
uint8 ACTION_VTOL_TRANSITION_TO_MULTICOPTER = 6
uint8 ACTION_VTOL_TRANSITION_TO_FIXEDWING = 7

uint8 source # 请求是如何触发的
uint8 SOURCE_STICK_GESTURE = 0
uint8 SOURCE_RC_SWITCH = 1
uint8 SOURCE_RC_BUTTON = 2
uint8 SOURCE_RC_MODE_SLOT = 3

uint8 mode # 对于ACTION_SWITCH_MODE，根据vehicle_status_s::NAVIGATION_STATE_*请求了什么模式
```