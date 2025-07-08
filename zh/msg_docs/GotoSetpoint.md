# GotoSetpoint（UORB消息）

位置和（可选）航向设定点及其对应的速度约束  
设定点分别作为位置和航向平滑器的输入  
设定点不需要满足运动学一致性  
可选航向设定点可通过相应标志位控制  
未设置的可选设定点将不被控制  
未设置的可选约束将默认使用机体规格

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/GotoSetpoint.msg)

```c
# 位置和（可选）航向设定点及其对应的速度约束
# 设定点分别作为位置和航向平滑器的输入
# 设定点不需要满足运动学一致性
# 可选航向设定点可通过相应标志位控制
# 未设置的可选设定点将不被控制
# 未设置的可选约束将默认使用机体规格

uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动后经过的时间（微秒）

# 设定点
float32[3] position # [m] NED本地世界坐标系

bool flag_control_heading # 如果需要控制航向则为true
float32 heading # (可选) [rad] [-pi,pi] 来自北方的航向角

# 约束
bool flag_set_max_horizontal_speed # 如果设置非默认水平速度限制则为true
float32 max_horizontal_speed # (可选) [m/s] NE平面的最大绝对速度

bool flag_set_max_vertical_speed # 如果设置非默认垂直速度限制则为true
float32 max_vertical_speed # (可选) [m/s] D轴的最大绝对速度

bool flag_set_max_heading_rate # 如果设置非默认航向率限制则为true
float32 max_heading_rate # (可选) [rad/s] 最大绝对航向率
```