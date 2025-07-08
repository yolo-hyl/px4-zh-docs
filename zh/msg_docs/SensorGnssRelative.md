# SensorGnssRelative（UORB消息）

GNSS相对定位信息（NED坐标系）。NED坐标系被定义为参考站处的本地拓扑系统。

[源文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorGnssRelative.msg)

```c
# GNSS相对定位信息（NED坐标系）。NED坐标系被定义为参考站处的本地拓扑系统。

uint64 timestamp                  # 系统启动后的时间（微秒）
uint64 timestamp_sample           # 系统启动后的时间（微秒）

uint32 device_id                  # 传感器的唯一设备ID，断电后保持不变

uint64 time_utc_usec              # 时间戳（微秒，UTC），这是从GPS模块获取的时间戳。在冷启动后可能不可用，此时值为0

uint16 reference_station_id       # 参考站ID

float32[3] position               # GPS NED相对位置向量（米）
float32[3] position_accuracy      # 相对位置精度（米）

float32 heading                   # 相对位置向量的航向（弧度）
float32 heading_accuracy          # 相对位置向量的航向精度（弧度）

float32 position_length           # 位置向量长度（米）
float32 accuracy_length           # 位置长度精度（米）

bool gnss_fix_ok                  # GNSS有效定位（即在DOP和精度掩码范围内）
bool differential_solution        # 已应用差分校正
bool relative_position_valid
bool carrier_solution_floating    # 载波相位测距解算（浮动模糊度）
bool carrier_solution_fixed       # 载波相位测距解算（固定模糊度）
bool moving_base_mode             # 接收机是否处于移动基座模式
bool reference_position_miss      # 使用外推参考位置计算移动基座解算（当前周期）
bool reference_observations_miss  # 使用外推参考观测值计算移动基座解算（当前周期）
bool heading_valid
bool relative_position_normalized # 相对位置向量的组件（包括高精度部分）已归一化
```