# 传感器预飞行检查（UORB 消息）

预飞行传感器检查指标。
当机体处于武装状态时，该主题将不再更新

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorPreflightMag.msg)

```c
#
# Pre-flight sensor check metrics.
# The topic will not be updated when the vehicle is armed
#
uint64 timestamp # time since system start (microseconds)

float32 mag_inconsistency_angle # maximum angle between magnetometer instance field vectors in radians.

```