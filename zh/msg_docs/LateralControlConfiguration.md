# 横向控制配置（UORB消息）

固定翼横向控制配置消息  
由fw_lateral_longitudinal_control模块用于约束FixedWingLateralSetpoint消息。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/LateralControlConfiguration.msg)

```c
# 固定翼横向控制配置消息  
# 由fw_lateral_longitudinal_control模块用于约束FixedWingLateralSetpoint消息  

uint32 MESSAGE_VERSION = 0  

uint64 timestamp          # 自系统启动以来的时间（微秒）  

float32 lateral_accel_max # [m/s²] 当前映射到最大滚转角，accel_max = tan(roll_max) * 重力加速度  
```