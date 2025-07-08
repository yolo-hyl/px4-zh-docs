# ActuatorServosTrim (UORB消息)

舵机微调，作为偏移量添加到舵机输出

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorServosTrim.msg)

```c
# 舵机微调，作为偏移量添加到舵机输出
uint64 timestamp			# 自系统启动以来的时间（微秒）

uint8 NUM_CONTROLS = 8
float32[8] trim    # 范围: [-1, 1]
```