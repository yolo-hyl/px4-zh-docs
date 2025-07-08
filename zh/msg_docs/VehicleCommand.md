# 机体指令 (uORB消息)

机体指令 uORB消息。用于指挥任务/动作等。
遵循MAVLink的COMMAND_INT / COMMAND_LONG定义

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/VehicleCommand.msg)

```c

```# 机体指令uORB消息。用于指挥任务/动作等。以下是基于提供的MAVLink命令列表的分类总结，按功能和用途进行组织：

---

### **飞行控制与安全**
- **VEHICLE_CMD_COMPONENT_ARM_DISARM**  
  武器/禁用组件。  
  *参数*: 1（1：启用，0：禁用）。

- **VEHICLE_CMD_RUN_PREARM_CHECKS**  
  请求目标系统运行预启用检查。

- **VEHICLE_CMD_ARM_AUTHORIZATION_REQUEST**  
  请求启用授权。

---

### **导航与任务**
- **VEHICLE_CMD_MISSION_START**  
  开始执行任务。  
  *参数*: 第一个任务项和最后一个任务项。

- **VEHICLE_CMD_DO_VTOL_TRANSITION**  
  VTOL飞行器模式转换（固定翼/多旋翼）。

- **VEHICLE_CMD_ACTUATOR_TEST**  
  执行舵机测试。  
  *参数*: 测试值、超时时间、输出功能。

---

### **传感器与校准**
- **VEHICLE_CMD_PREFLIGHT_CALIBRATION**  
  触发校准（仅在预飞模式下接受）。  
  *参数*: 校准类型（如温控校准）。

- **VEHICLE_CMD_PREFLIGHT_SET_SENSOR_OFFSETS**  
  设置传感器偏移（加速度计、磁力计等）。  
  *参数*: 传感器类型和各轴偏移值。

- **VEHICLE_CMD_FIXED_MAG_CAL_YAW**  
  基于已知偏航角的磁力计校准。  
  *参数*: 已知偏航角、经纬度（若为0则使用当前位置）。

- **VEHICLE_CMD_EXTERNAL_POSITION_ESTIMATE**  
  外部估计器全局位置重置（用于死记位置）。

- **VEHICLE_CMD_EXTERNAL_WIND_ESTIMATE**  
  外部风速估计。

---

### **相机与视频**
- **VEHICLE_CMD_SET_CAMERA_MODE**  
  设置相机模式（拍照、录像等）。

- **VEHICLE_CMD_SET_CAMERA_ZOOM**  
  设置相机变焦。

- **VEHICLE_CMD_SET_CAMERA_FOCUS**  
  设置相机对焦。

- **VEHICLE_CMD_IMAGE_START_CAPTURE**  
  开始图像捕获序列。

- **VEHICLE_CMD_VIDEO_START_CAPTURE / VEHICLE_CMD_VIDEO_STOP_CAPTURE**  
  启动/停止视频捕获。

- **VEHICLE_CMD_DO_TRIGGER_CONTROL**  
  启用/禁用相机触发系统。

- **VEHICLE_CMD_OBLIQUE_SURVEY**  
  设置相机自动倾斜摄影测量任务。  
  *参数*: 拍摄间隔、快门时间、倾斜角度等。

---

### **云台管理**
- **VEHICLE_CMD_DO_GIMBAL_MANAGER_PITCHYAW**  
  设置云台俯仰和偏航角度。

- **VEHICLE_CMD_DO_GIMBAL_MANAGER_CONFIGURE**  
  配置云台管理器的主/次控制系统。

- **VEHICLE_CMD_GIMBAL_DEVICE_INFORMATION**  
  请求低级云台设备信息。

---

### **预飞配置与系统管理**
- **VEHICLE_CMD_PREFLIGHT_UAVCAN**  
  UAVCAN配置（如启动舵机映射）。

- **VEHICLE_CMD_PREFLIGHT_STORAGE**  
  请求参数/任务存储（读取或写入）。

- **VEHICLE_CMD_PREFLIGHT_REBOOT_SHUTDOWN**  
  请求重启或关闭系统组件（自动驾驶仪、计算单元）。

- **VEHICLE_CMD_INJECT_FAILURE**  
  注入人工故障（用于测试）。

---

### **通信与数据**
- **VEHICLE_CMD_REQUEST_MESSAGE**  
  请求发送特定MAVLink消息。

- **VEHICLE_CMD_LOGGING_START / VEHICLE_CMD_LOGGING_STOP**  
  启动/停止ULog数据流。

- **VEHICLE_CMD_CONTROL_HIGH_LATENCY**  
  控制高延迟链路的数据传输。

---

### **设备控制**
- **VEHICLE_CMD_DO_WINCH**  
  操作绞车（收线/放线）。

- **VEHICLE_CMD_PAYLOAD_PREPARE_DEPLOY / VEHICLE_CMD_PAYLOAD_CONTROL_DEPLOY**  
  准备并控制负载部署。

- **VEHICLE_CMD_START_RX_PAIR**  
  启动接收器配对（如Spektrum DSM2/DSMX）。

---

### **特殊用途**
- **VEHICLE_CMD_DO_GIMBAL_MANAGER_PITCHYAW**  
  云台角度设置（独立于任务）。

- **VEHICLE_CMD_DO_VTOL_TRANSITION**  
  VTOL飞行器模式切换。

---

### **占位符与标记**
- **VEHICLE_CMD_PREFLIGHT_CALIBRATION**  
  （参数3为温度校准）

- **VEHICLE_CMD_DO_LAST / VEHICLE_CMD_PREFLIGHT_CALIBRATION**  
  标记命令的上下限，无实际功能。

---

### **注意事项**
- **编号范围**：命令编号从241到43004，部分由扩展功能（如43003-43004）定义。
- **参数格式**：参数通常以`param1`, `param2`等表示，部分命令包含多个参数（如传感器偏移）。
- **安全性**：涉及启用/禁用的命令（如`VEHICLE_CMD_COMPONENT_ARM_DISARM`）通常需要严格权限控制。

此分类可帮助快速定位所需命令，适用于开发、调试或文档整理场景。# PX4 机体命令（超出16位 MAVLink 命令范围）
uint32 VEHICLE_CMD_PX4_INTERNAL_START = 65537 # PX4 内部专用机体命令起始值（> UINT16_MAX）。
uint32 VEHICLE_CMD_SET_GPS_GLOBAL_ORIGIN = 100000 # 设置机体本地原点（0,0,0）的GPS坐标。 |未使用|未使用|未使用|未使用|纬度（WGS-84）|经度（WGS-84）|[m]海拔（相对GNSS的绝对高度，正数代表高于地面）|
uint32 VEHICLE_CMD_SET_NAV_STATE = 100001 # 通过直接指定导航状态来切换模式。 |导航状态|未使用|未使用|未使用|未使用|未使用|未使用|

uint8 VEHICLE_MOUNT_MODE_RETRACT = 0 # 从永久存储读取并保持安全位置（翻滚角/俯仰角/偏航角），停止稳定控制。
uint8 VEHICLE_MOUNT_MODE_NEUTRAL = 1 # 从永久存储读取并保持中立位置（翻滚角/俯仰角/偏航角）。
uint8 VEHICLE_MOUNT_MODE_MAVLINK_TARGETING = 2 # 读取中立位置并启动MAVLink翻滚角/俯仰角/偏航角控制（带稳定）。
uint8 VEHICLE_MOUNT_MODE_RC_TARGETING = 3 # 读取中立位置并启动遥控翻滚角/俯仰角/偏航角控制（带稳定）。
uint8 VEHICLE_MOUNT_MODE_GPS_POINT = 4 # 读取中立位置并开始指向指定经纬高坐标。
uint8 VEHICLE_MOUNT_MODE_ENUM_END = 5 #

uint8 VEHICLE_ROI_NONE = 0 # 无兴趣区域。
uint8 VEHICLE_ROI_WPNEXT = 1 # 指向下一个任务航点。
uint8 VEHICLE_ROI_WPINDEX = 2 # 指向指定任务航点。
uint8 VEHICLE_ROI_LOCATION = 3 # 指向固定位置。
uint8 VEHICLE_ROI_TARGET = 4 # 指向目标。
uint8 VEHICLE_ROI_ENUM_END = 5

uint8 PARACHUTE_ACTION_DISABLE = 0
uint8 PARACHUTE_ACTION_ENABLE = 1
uint8 PARACHUTE_ACTION_RELEASE = 2

uint8 FAILURE_UNIT_SENSOR_GYRO = 0
uint8 FAILURE_UNIT_SENSOR_ACCEL = 1
uint8 FAILURE_UNIT_SENSOR_MAG = 2
uint8 FAILURE_UNIT_SENSOR_BARO = 3
uint8 FAILURE_UNIT_SENSOR_GPS = 4
uint8 FAILURE_UNIT_SENSOR_OPTICAL_FLOW = 5
uint8 FAILURE_UNIT_SENSOR_VIO = 6
uint8 FAILURE_UNIT_SENSOR_DISTANCE_SENSOR = 7
uint8 FAILURE_UNIT_SENSOR_AIRSPEED = 8
uint8 FAILURE_UNIT_SYSTEM_BATTERY = 100
uint8 FAILURE_UNIT_SYSTEM_MOTOR = 101
uint8 FAILURE_UNIT_SYSTEM_SERVO = 102
uint8 FAILURE_UNIT_SYSTEM_AVOIDANCE = 103
uint8 FAILURE_UNIT_SYSTEM_RC_SIGNAL = 104
uint8 FAILURE_UNIT_SYSTEM_MAVLINK_SIGNAL = 105

uint8 FAILURE_TYPE_OK = 0
uint8 FAILURE_TYPE_OFF = 1
uint8 FAILURE_TYPE_STUCK = 2
uint8 FAILURE_TYPE_GARBAGE = 3
uint8 FAILURE_TYPE_WRONG = 4
uint8 FAILURE_TYPE_SLOW = 5
uint8 FAILURE_TYPE_DELAYED = 6
uint8 FAILURE_TYPE_INTERMITTENT = 7# 用作 DO_CHANGE_SPEED 命令中的 param1 参数。

uint8 SPEED_TYPE_AIRSPEED = 0  
uint8 SPEED_TYPE_GROUNDSPEED = 1  
uint8 SPEED_TYPE_CLIMB_SPEED = 2  
uint8 SPEED_TYPE_DESCEND_SPEED = 3# 用作 CMD_DO_ORBIT 中的 param3
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_FRONT_TO_CIRCLE_CENTER = 0
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_INITIAL_HEADING = 1
uint8 ORBIT_YAW_BEHAVIOUR_UNCONTROLLED = 2
uint8 ORBIT_YAW_BEHAVIOUR_HOLD_FRONT_TANGENT_TO_CIRCLE = 3
uint8 ORBIT_YAW_BEHAVIOUR_RC_CONTROLLED = 4
uint8 ORBIT_YAW_BEHAVIOUR_UNCHANGED = 5# 用作ARM_DISARM命令中的参数1。
int8 ARMING_ACTION_DISARM = 0
int8 ARMING_ACTION_ARM = 1# param2 在 VEHICLE_CMD_DO_GRIPPER 中。
uint8 GRIPPER_ACTION_RELEASE = 0
uint8 GRIPPER_ACTION_GRAB = 1

uint8 ORB_QUEUE_LENGTH = 8

float32 param1 # 参数1，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float32 param2 # 参数2，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float32 param3 # 参数3，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float32 param4 # 参数4，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float64 param5 # 参数5，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float64 param6 # 参数6，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
float32 param7 # 参数7，如MAVLink uint16 VEHICLE_CMD枚举中定义的。
uint32 command # 命令ID。
uint8 target_system # 应执行命令的系统。
uint8 target_component # 应执行命令的组件，0表示所有组件。
uint8 source_system # 发送命令的系统。
uint16 source_component # 发送命令的组件/模式执行器。
uint8 confirmation # 0: 此命令的首次传输。1-255: 确认传输（例如用于杀伤命令）。
bool from_external

uint16 COMPONENT_MODE_EXECUTOR_START = 1000# 主题 vehicle_command gimbal_v1_command 机体指令模式执行器