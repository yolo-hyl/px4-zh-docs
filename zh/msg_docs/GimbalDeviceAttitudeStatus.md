# 云台设备姿态状态 (UORB 消息)


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GimbalDeviceAttitudeStatus.msg)

```c
uint64 timestamp						# 系统启动后经过的时间（微秒）

uint8 target_system
uint8 target_component
uint16 device_flags

uint16 DEVICE_FLAGS_RETRACT = 1
uint16 DEVICE_FLAGS_NEUTRAL = 2
uint16 DEVICE_FLAGS_ROLL_LOCK = 4
uint16 DEVICE_FLAGS_PITCH_LOCK = 8
uint16 DEVICE_FLAGS_YAW_LOCK = 16

float32[4] q
float32 angular_velocity_x
float32 angular_velocity_y
float32 angular_velocity_z

uint32 failure_flags
float32 delta_yaw
float32 delta_yaw_velocity
uint8 gimbal_device_id

bool received_from_mavlink

```