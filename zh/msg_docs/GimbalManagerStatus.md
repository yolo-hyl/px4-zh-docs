# 云台管理器状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/GimbalManagerStatus.msg)

```c
uint64 timestamp		# 系统启动后时间（微秒）

uint32 flags
uint8 gimbal_device_id
uint8 primary_control_sysid
uint8 primary_control_compid
uint8 secondary_control_sysid
uint8 secondary_control_compid

```