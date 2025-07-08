# VtolVehicleStatus (UORB消息)

VEHICLE_VTOL_STATE，应与MAVLink的MAV_VTOL_STATE一一对应

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VtolVehicleStatus.msg)

```c
# VEHICLE_VTOL_STATE，应与MAVLink的MAV_VTOL_STATE一一对应

uint32 MESSAGE_VERSION = 0

uint8 VEHICLE_VTOL_STATE_UNDEFINED = 0
uint8 VEHICLE_VTOL_STATE_TRANSITION_TO_FW = 1
uint8 VEHICLE_VTOL_STATE_TRANSITION_TO_MC = 2
uint8 VEHICLE_VTOL_STATE_MC = 3
uint8 VEHICLE_VTOL_STATE_FW = 4

uint64 timestamp			# 系统启动后的时间（微秒）

uint8 vehicle_vtol_state		# vtol当前状态，参见VEHICLE_VTOL_STATE

bool fixed_wing_system_failure		# 机体处于固定翼系统故障保护模式（quad-chute后）

```