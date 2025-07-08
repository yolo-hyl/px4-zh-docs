# 轨道状态 (UORB 消息)

ORBIT_YAW_BEHAVIOUR

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/OrbitStatus.msg)

```c
# ORBIT_YAW_BEHAVIOUR
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_FRONT_TO_CIRCLE_CENTER = 0
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_INITIAL_HEADING = 1
uint8 ORBIT_YAW_BEHAVIOUR_UNCONTROLLED = 2
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_FRONT_TANGENT_TO_CIRCLE = 3
uint8 ORBIT_YAW_BEHAVIOUR_RC_CONTROLLED = 4
uint8 ORBIT_YAW_BEHAVIOUR_UNCHANGED = 5

uint64 timestamp # 自系统启动以来的时间（微秒）
float32 radius   # 轨道圆半径。正值表示顺时针环绕，负值表示逆时针环绕。[m]
uint8 frame      # 字段x/y/z的坐标系统
float64 x        # 圆心X坐标。坐标系统取决于frame字段：local = 本地坐标（米 * 1e4），global = 纬度（度 * 1e7）
float64 y        # 圆心Y坐标。坐标系统取决于frame字段：local = 本地坐标（米 * 1e4），global = 纬度（度 * 1e7）
float32 z        # 圆心海拔高度。坐标系统取决于frame字段
uint8 yaw_behaviour
```