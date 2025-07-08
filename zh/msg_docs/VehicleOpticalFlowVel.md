# 机体OpticalFlowVel (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleOpticalFlowVel.msg)

```c
uint64 timestamp                       # 系统启动后经过时间 (微秒)
uint64 timestamp_sample                # 原始数据的时间戳 (微秒)

float32[2] vel_body                    # 通过陀螺仪补偿和距离缩放处理的光流原始测量值在机体坐标系下的速度(m/s)
float32[2] vel_ne                      # 与vel_body相同但位于局部坐标系下的速度(m/s)

float32[2] vel_body_filtered           # 经过滤波处理的陀螺仪补偿和距离缩放光流原始测量值在机体坐标系下的速度(m/s)
float32[2] vel_ne_filtered             # 与vel_body_filtered相同但位于局部坐标系下的速度(m/s)

float32[2] flow_rate_uncompensated     # 集成光流测量值 (rad/s)
float32[2] flow_rate_compensated       # 经过角运动补偿的集成光流测量值 (rad/s)

float32[3] gyro_rate                   # 与光流测量同步的陀螺仪测量值 (rad/s)

float32[3] gyro_bias
float32[3] ref_gyro

# TOPICS estimator_optical_flow_vel vehicle_optical_flow_vel
```