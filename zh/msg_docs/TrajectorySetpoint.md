# 轨迹设定点 (UORB 消息)

NED坐标系下的轨迹设定点  
PID位置控制器的输入  
需要满足运动学一致性以实现平滑飞行  
将值设为NaN表示该状态不应被控制  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/TrajectorySetpoint.msg)

```c
# NED坐标系下的轨迹设定点  
# PID位置控制器的输入  
# 需要满足运动学一致性以实现平滑飞行  
# 将值设为NaN表示该状态不应被控制  

uint32 MESSAGE_VERSION = 0  

uint64 timestamp # 系统启动后经过的时间（微秒）  

# NED局部世界坐标系  
float32[3] position # 单位：米  
float32[3] velocity # 单位：米/秒  
float32[3] acceleration # 单位：米/秒²  
float32[3] jerk # 单位：米/秒³（仅用于记录）  

float32 yaw # 期望姿态的欧拉角（弧度制，-PI..+PI）  
float32 yawspeed # 相对于NED坐标系z轴的角速度（弧度/秒）  
```