# 执行器控制状态 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorControlsStatus.msg)

```c
uint64 timestamp			# 系统启动后的时间（微秒）

float32[3] control_power

# TOPICS actuator_controls_status_0 actuator_controls_status_1

```