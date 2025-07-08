# 轮胎编码器 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/WheelEncoders.msg)

```c
uint64 timestamp			# 系统启动后的时间（微秒）

# 两个轮子：0 右，1 左
float32[2] wheel_speed # [弧度/秒]
float32[2] wheel_angle # [弧度]
```