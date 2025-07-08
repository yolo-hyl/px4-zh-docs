# TrajectorySetpoint6dof (UORB消息)

北东地坐标系下的轨迹设定点  
位置控制器的输入  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TrajectorySetpoint6dof.msg)

```c
# 北东地坐标系下的轨迹设定点  
# 位置控制器的输入  

uint64 时间戳 # 系统启动后的时间（微秒）  

# 北东地本地世界坐标系  
float32[3] position               # 以米为单位  
float32[3] velocity               # 以米/秒为单位  
float32[3] acceleration           # 以米/秒²为单位  
float32[3] jerk                   # 以米/秒³为单位（仅用于记录）  

float32[4] quaternion             # 单位四元数  
float32[3] angular_velocity       # 以弧度/秒为单位的角速度  
```