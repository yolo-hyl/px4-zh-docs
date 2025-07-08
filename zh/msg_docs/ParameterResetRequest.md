# ParameterResetRequest (UORB消息)

ParameterResetRequest : 由主控用以在远程重置一个或全部参数值

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ParameterResetRequest.msg)

```c
# ParameterResetRequest : 由主控用以在远程重置一个或全部参数值

uint64 timestamp
uint16 parameter_index

bool reset_all              # 如果此值为真，则忽略 parameter_index

uint8 ORB_QUEUE_LENGTH = 4

```