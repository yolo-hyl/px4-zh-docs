# 云台设备信息(UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GimbalDeviceInformation.msg)

```c
uint64 timestamp						# 系统启动后时间（微秒）

uint8[32] vendor_name
uint8[32] model_name
uint8[32] custom_name
uint32 firmware_version
uint32 hardware_version
uint64 uid

uint16 cap_flags

uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_RETRACT = 1
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_NEUTRAL = 2
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_ROLL_AXIS = 4
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_ROLL_FOLLOW = 8
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_ROLL_LOCK = 16
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_PITCH_AXIS = 32
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_PITCH_FOLLOW = 64
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_PITCH_LOCK = 128
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_YAW_AXIS = 256
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_YAW_FOLLOW = 512
uint32 GIMBAL_DEVICE_CAP_FLAGS_HAS_YAW_LOCK = 1024
uint32 GIMBAL_DEVICE_CAP_FLAGS_SUPPORTS_INFINITE_YAW = 2048

uint16 custom_cap_flags

float32 roll_min # [弧度]
float32 roll_max # [弧度]

float32 pitch_min # [弧度]
float32 pitch_max # [弧度]

float32 yaw_min # [弧度]
float32 yaw_max # [弧度]

uint8 gimbal_device_id

```