# 机外控制模式 (UORB消息)

机外控制模式  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/OffboardControlMode.msg)

```c
# 机外控制模式

uint64 timestamp		# 自系统启动以来的时间（微秒）

bool position
bool velocity
bool acceleration
bool attitude
bool body_rate
bool thrust_and_torque
bool direct_actuator

```