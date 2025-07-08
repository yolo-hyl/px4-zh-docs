# 速度限制 (UORB 消息)

多旋翼位置慢速模式的速度和偏航率限制

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VelocityLimits.msg)

```c
# Velocity and yaw rate limits for a multicopter position slow mode only

uint64 timestamp # 时间自系统启动以来的时间（微秒）

# 绝对速度，NAN 表示使用默认限制
float32 horizontal_velocity # [m/s]
float32 vertical_velocity # [m/s]
float32 yaw_rate # [rad/s]
```