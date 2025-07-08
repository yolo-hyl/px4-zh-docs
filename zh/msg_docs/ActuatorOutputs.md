# 执行器输出 (UORB 消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ActuatorOutputs.msg)

```c
uint64 timestamp				# 自系统启动以来的时间（微秒）
uint8 NUM_ACTUATOR_OUTPUTS		= 16
uint8 NUM_ACTUATOR_OUTPUT_GROUPS	= 4	# 用于合理性检查
uint32 noutputs				# 有效输出
float32[16] output				# 输出数据，以自然输出单位表示

# actuator_outputs_sim 用于 SITL、HITL & SIH（输出范围为 [-1, 1]）
# 主题 actuator_outputs actuator_outputs_sim actuator_outputs_debug
```