# ParameterSetValueRequest（UORB消息）

ParameterSetValueRequest：由远程或主控用于更新另一端参数的值  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ParameterSetValueRequest.msg)  

```c
# ParameterSetValueRequest：由远程或主控用于更新另一端参数的值  

uint64 timestamp  
uint16 parameter_index  

int32 int_value             # 整数参数的可选值  
float32 float_value         # 浮点参数的可选值  

uint8 ORB_QUEUE_LENGTH = 32  

# TOPICS parameter_set_value_request parameter_remote_set_value_request parameter_primary_set_value_request  
```