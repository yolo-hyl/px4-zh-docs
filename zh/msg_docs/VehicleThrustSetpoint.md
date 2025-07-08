# 机体推力设定值 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleThrustSetpoint.msg)

uint64 timestamp        # 系统启动以来的时间（微秒）
uint64 timestamp_sample # 该消息所基于的数据样本的时间戳（微秒）

float32[3] xyz          # 沿机体 X、Y、Z 轴的推力设定值 [-1, 1]

# 主题 机体推力设定值
# 主题 机体推力设定值虚拟前向 机体推力设定值虚拟多旋翼