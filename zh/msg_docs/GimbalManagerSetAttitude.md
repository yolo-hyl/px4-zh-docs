# 云台管理器设置姿态 (UORB message)  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GimbalManagerSetAttitude.msg)  

```c
uint64 timestamp						# 系统启动后的时间（微秒）  

uint8 origin_sysid  
uint8 origin_compid  

uint8 target_system  
uint8 target_component  

uint32 GIMBAL_MANAGER_FLAGS_RETRACT = 1  
uint32 GIMBAL_MANAGER_FLAGS_NEUTRAL = 2  
uint32 GIMBAL_MANAGER_FLAGS_ROLL_LOCK = 4  
uint32 GIMBAL_MANAGER_FLAGS_PITCH_LOCK = 8  
uint32 GIMBAL_MANAGER_FLAGS_YAW_LOCK = 16  

uint32 flags  
uint8 gimbal_device_id  

float32[4] q  

float32 angular_velocity_x  
float32 angular_velocity_y  
float32 angular_velocity_z  

uint8 ORB_QUEUE_LENGTH = 2  
```