# 起飞状态 (UORB消息)

起飞状态机的状态，目前仅适用于多旋翼机

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TakeoffStatus.msg)

```c
# Status of the takeoff state machine currently just available for multicopters

uint64 timestamp # time since system start (microseconds)

uint8 TAKEOFF_STATE_UNINITIALIZED     = 0
uint8 TAKEOFF_STATE_DISARMED          = 1
uint8 TAKEOFF_STATE_SPOOLUP           = 2
uint8 TAKEOFF_STATE_READY_FOR_TAKEOFF = 3
uint8 TAKEOFF_STATE_RAMPUP            = 4
uint8 TAKEOFF_STATE_FLIGHT            = 5

uint8 takeoff_state

float32 tilt_limit # limited tilt feasibility during takeoff, contains maximum tilt otherwise

```