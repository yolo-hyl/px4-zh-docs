# UavcanParameterValue (UORB message)

UAVCAN-MAVLink参数桥接响应类型

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/UavcanParameterValue.msg)

```c
# UAVCAN-MAVLink参数桥接响应类型
uint64 timestamp		# 系统启动后经过的时间（微秒）
uint8 node_id			# 从MAVLink组件ID映射的UAVCAN节点ID
char[17] param_id		# MAVLink/UAVCAN参数名称
int16 param_index		# 参数索引（已知时）
uint16 param_count		# 节点暴露的参数数量
uint8 param_type		# MAVLink参数类型
int64 int_value			# 当param_type为整型时的当前值
float32 real_value		# 当param_type为浮点型时的当前值
```