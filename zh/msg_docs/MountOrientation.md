# 安装方向 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MountOrientation.msg)

```c
uint64 timestamp				# 自系统启动以来的时间（微秒）
float32[3] attitude_euler_angle		# 安装方向的欧拉角（弧度）
```