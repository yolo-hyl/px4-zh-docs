# 电源按钮状态 (UORB消息)

电源按钮状态通知消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/PowerButtonState.msg)

```c
# 电源按钮状态通知消息

uint64 timestamp			    # 系统启动以来的时间（微秒）

uint8 PWR_BUTTON_STATE_IDEL = 0             # 按钮弹起但未达到关机按钮按下时间（删除事件）
uint8 PWR_BUTTON_STATE_DOWN = 1             # 按钮按下
uint8 PWR_BUTTON_STATE_UP = 2               # 按钮弹起
uint8 PWR_BUTTON_STATE_REQUEST_SHUTDOWN = 3 # 按钮弹起后达到关机按钮按下时间

uint8 event                                 # 为 PWR_BUTTON_STATE_* 中的值

```