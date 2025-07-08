# 机体角加速度设定值 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleAngularAccelerationSetpoint.msg)

```c
uint64 timestamp         # 自系统启动以来的时间（微秒）
uint64 timestamp_sample  # 此消息所基于的数据样本的时间戳（微秒）

float32[3] xyz           # X、Y、Z机体轴上的角加速度（rad/s²）
```