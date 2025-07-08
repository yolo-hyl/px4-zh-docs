# 机体扭矩设定点 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleTorqueSetpoint.msg)

```c
uint64 timestamp        # 系统启动后时间 (微秒)
uint64 timestamp_sample # 本消息所基于的数据样本时间戳 (微秒)

float32[3] xyz          # 关于X、Y、Z机体轴的扭矩设定点 (归一化)

# TOPICS vehicle_torque_setpoint
# TOPICS vehicle_torque_setpoint_virtual_fw vehicle_torque_setpoint_virtual_mc
```