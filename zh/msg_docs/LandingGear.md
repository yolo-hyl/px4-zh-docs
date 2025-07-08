# 起落架 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LandingGear.msg)

```c
uint64 timestamp # 系统启动后的时间（微秒）

int8 GEAR_UP = 1 # 起落架收起
int8 GEAR_DOWN = -1 # 起落架放下
int8 GEAR_KEEP = 0 # 保持当前状态

int8 landing_gear
```