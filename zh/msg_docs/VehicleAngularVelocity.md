# 机体角速度 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleAngularVelocity.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp          # 系统启动后经过的时间（微秒）
uint64 timestamp_sample   # 本消息所基于的数据采样时间戳（微秒）

float32[3] xyz		  # 机体FRD坐标系XYZ轴的偏置校正角速度（弧度/秒）

float32[3] xyz_derivative # 机体FRD坐标系XYZ轴的角加速度（弧度/秒²）

# TOPICS vehicle_angular_velocity vehicle_angular_velocity_groundtruth

```