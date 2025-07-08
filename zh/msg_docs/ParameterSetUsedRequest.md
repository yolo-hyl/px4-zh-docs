# ParameterSetUsedRequest (UORB message)

ParameterSetUsedRequest : 由远程设备用于更新主控上参数的已使用标志位

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ParameterSetUsedRequest.msg)

```c
# ParameterSetUsedRequest : 由远程设备用于更新主控上参数的已使用标志位

uint64 timestamp
uint16 parameter_index

uint8 ORB_QUEUE_LENGTH = 64

```