# VehicleStatusV0 (UORB消息)

编码由commander发布的机体系统状态

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/px4_msgs_old/msg/VehicleStatusV0.msg)

```c
# 编码由commander发布的机体系统状态

uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动后经过的时间（微秒）

uint64 armed_time # 解锁时间戳（微秒）
uint64 takeoff_time # 起飞时间戳（微秒）

uint8 arming_state
uint8 ARMING_STATE_DISARMED = 1
uint8 ARMING_STATE_ARMED    = 2

uint8 latest_arming_reason
uint8 latest_disarming_reason
uint8 ARM_DISARM_REASON_TRANSITION_TO_STANDBY = 0
uint8 ARM_DISARM_REASON_STICK_GESTURE = 1
uint8 ARM_DISARM_REASON_RC_SWITCH = 2
uint8 ARM_DISARM_REASON_COMMAND_INTERNAL = 3
uint8 ARM_DISARM_REASON_COMMAND_EXTERNAL = 4
uint8 ARM_DISARM_REASON_MISSION_START = 5
uint8 ARM_DISARM_REASON_SAFETY_BUTTON = 6
uint8 ARM_DISARM_REASON_AUTO_DISARM_LAND = 7
uint8 ARM_DISARM_REASON_AUTO_DISARM_PREFLIGHT = 8
uint8 ARM_DISARM_REASON_KILL_SWITCH = 9
uint8 ARM_DISARM_REASON_LOCKDOWN = 10
uint8 ARM_DISARM_REASON_FAILURE_DETECTOR = 11
uint8 ARM_DISARM_REASON_SHUTDOWN = 12
uint8 ARM_DISARM_REASON_UNIT_TEST = 13

uint64 nav_state_timestamp # 当前nav_state激活的时间

uint8 nav_state_user_intention                  # 用户选择的模式（在安全保护情况下可能与nav_state不同）

uint8 nav_state                                 # 当前激活的模式
uint8 NAVIGATION_STATE_MANUAL = 0               # 手动模式
uint8 NAVIGATION_STATE_ALTCTL = 1               # 高度控制模式
uint8 NAVIGATION_STATE_POSCTL = 2               # 位置控制模式
uint8 NAVIGATION_STATE_AUTO_MISSION = 3         # 自动任务模式
uint8 NAVIGATION_STATE_AUTO_LOITER = 4          # 自动盘旋模式
uint8 NAVIGATION_STATE_AUTO_RTL = 5             # 自动返航模式
uint8 NAVIGATION_STATE_POSITION_SLOW = 6
uint8 NAVIGATION_STATE_FREE5 = 7
uint8 NAVIGATION_STATE_FREE4 = 8
uint8 NAVIGATION_STATE_FREE3 = 9
uint8 NAVIGATION_STATE_ACRO = 10                # 特技飞行模式
uint8 NAVIGATION_STATE_FREE2 = 11
uint8 NAVIGATION_STATE_DESCEND = 12             # 下降模式（无位置控制）
uint8 NAVIGATION_STATE_TERMINATION = 13         # 终止模式
uint8 NAVIGATION_STATE_OFFBOARD = 14
uint8 NAVIGATION_STATE_STAB = 15                # 稳定模式
uint8 NAVIGATION_STATE_FREE1 = 16
uint8 NAVIGATION_STATE_AUTO_TAKEOFF = 17        # 起飞
uint8 NAVIGATION_STATE_AUTO_LAND = 18           # 着陆
uint8 NAVIGATION_STATE_AUTO_FOLLOW_TARGET = 19  # 自动跟随
uint8 NAVIGATION_STATE_AUTO_PRECLAND = 20       # 精准着陆（使用着陆目标）
uint8 NAVIGATION_STATE_ORBIT = 21               # 绕圈飞行
uint8 NAVIGATION_STATE_AUTO_VTOL_TAKEOFF = 22   # 起飞、转换、建立盘旋
uint8 NAVIGATION_STATE_EXTERNAL1 = 23
uint8 NAVIGATION_STATE_EXTERNAL2 = 24
uint8 NAVIGATION_STATE_EXTERNAL3 = 25
uint8 NAVIGATION_STATE_EXTERNAL4 = 26
uint8 NAVIGATION_STATE_EXTERNAL5 = 27
uint8 NAVIGATION_STATE_EXTERNAL6 = 28
uint8 NAVIGATION_STATE_EXTERNAL7 = 29
uint8 NAVIGATION_STATE_EXTERNAL8 = 30
uint8 NAVIGATION_STATE_MAX = 31

uint8 executor_in_charge                        # 当前模式执行负责人（0=自动驾驶仪）

uint32 valid_nav_states_mask                    # 所有有效nav_state值的位掩码
uint32 can_set_nav_states_mask                  # 用户可选择模式的位掩码

# 检测到的故障位掩码
uint16 failure_detector_status
uint16 FAILURE_NONE = 0
uint16 FAILURE_ROLL = 1              # (1 << 0)
uint16 FAILURE_PITCH = 2             # (1 << 1)
uint16 FAILURE_ALT = 4               # (1 << 2)
uint16 FAILURE_EXT = 8               # (1 << 3)
uint16 FAILURE_ARM_ESC = 16          # (1 << 4)
uint16 FAILURE_BATTERY = 32          # (1 << 5)
uint16 FAILURE_IMBALANCED_PROP = 64  # (1 << 6)
uint16 FAILURE_MOTOR = 128           # (1 << 7)

uint8 hil_state
uint8 HIL_STATE_OFF = 0
uint8 HIL_STATE_ON = 1

# 如果是VTOL，当以多旋翼飞行时值为VEHICLE_TYPE_ROTARY_WING，当以固定翼飞行时值为VEHICLE_TYPE_FIXED_WING
uint8 vehicle_type
uint8 VEHICLE_TYPE_UNKNOWN = 0
uint8 VEHICLE_TYPE_ROTARY_WING = 1
uint8 VEHICLE_TYPE_FIXED_WING = 2
uint8 VEHICLE_TYPE_ROVER = 3
uint8 VEHICLE_TYPE_AIRSHIP = 4

uint8 FAILSAFE_DEFER_STATE_DISABLED = 0
uint8 FAILSAFE_DEFER_STATE_ENABLED = 1
uint8 FAILSAFE_DEFER_STATE_WOULD_FAILSAFE = 2 # 延迟安全保护，但会触发安全保护

bool failsafe # 如果系统处于安全保护状态（如：RTL、悬停、终止等）则为true
bool failsafe_and_user_took_over # 如果系统处于安全保护状态但用户已接管控制则为true
uint8 failsafe_defer_state # 取值为FAILSAFE_DEFER_STATE_*中的一个

# 链路丢失
bool gcs_connection_lost              # 与GCS的数据链路丢失
uint8 gcs_connection_lost_counter     # 记录GCS连接丢失事件次数
bool high_latency_data_link_lost # 如果高延迟数据链（如RockBlock Iridium 9603遥测模块）丢失则为true

# VTOL标志
bool is_vtol             # 如果系统具备VTOL能力则为true
bool is_vtol_tailsitter  # 如果系统在从多旋翼转换到固定翼时执行90°俯冲旋转则为true
bool in_transition_mode  # 如果VTOL正在执行转换则为true
bool in_transition_to_fw # 如果VTOL正在从多旋翼转换到固定翼则为true

# MAVLink识别
uint8 system_type  # 系统类型，包含mavlink的MAV_TYPE
uint8 system_id	   # 系统ID，包含MAVLink的系统ID字段
uint8 component_id # 子系统/组件ID，包含MAVLink的组件ID字段

bool safety_button_available # 如果连接了安全按钮则为true
bool safety_off # 如果安全开关关闭则为true

bool power_input_valid                            # 如果输入电源有效则设置为true
bool usb_connected                                # 当从USB链接接收到遥测数据时设置为true（永不清除）

bool open_drone_id_system_present
bool open_drone_id_system_healthy

bool parachute_system_present
bool parachute_system_healthy

bool failsafe # 如果系统处于安全保护状态（如：RTL、悬停、终止等）则为true
bool failsafe_and_user_took_over # 如果系统处于安全保护状态但用户已接管控制则为true
uint8 failsafe_defer_state # 取值为FAILSAFE_DEFER_STATE_*中的一个

# 链路丢失
bool gcs_connection_lost              # 与GCS的数据链路丢失
uint8 gcs_connection_lost_counter     # 记录GCS连接丢失事件次数
bool high_latency_data_link_lost # 如果高延迟数据链（如RockBlock Iridium 9603遥测模块）丢失则为true

# VTOL标志
bool is_vtol             # 如果系统具备VTOL能力则为true
bool is_vtol_tailsitter  # 如果系统在从多旋翼转换到固定翼时执行90°俯冲旋转则为true
bool in_transition_mode  # 如果VTOL正在执行转换则为true
bool in_transition_to_fw # 如果VTOL正在从多旋翼转换到固定翼则为true

# MAVLink识别
uint8 system_type  # 系统类型，包含mavlink的MAV_TYPE
uint8 system_id	   # 系统ID，包含MAVLink的系统ID字段
uint8 component_id # 子系统/组件ID，包含MAVLink的组件ID字段

bool safety_button_available # 如果连接了安全按钮则为true
bool safety_off # 如果安全开关关闭则为true

bool power_input_valid                            # 如果输入电源有效则设置为true
bool usb_connected                                # 当从USB链接接收到遥测数据时设置为true（永不清除）

bool open_drone_id_system_present
bool open_drone_id_system_healthy

bool parachute_system_present
bool parachute_system_healthy
```