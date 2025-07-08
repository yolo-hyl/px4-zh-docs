# 遥测状态 (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TelemetryStatus.msg)

```c
uint8 LINK_TYPE_GENERIC = 0
uint8 LINK_TYPE_UBIQUITY_BULLET = 1
uint8 LINK_TYPE_WIRE = 2
uint8 LINK_TYPE_USB = 3
uint8 LINK_TYPE_IRIDIUM	= 4

uint64 timestamp			# 系统启动后经过的时间（微秒）

uint8 type				# 无线电硬件类型（LINK_TYPE_*）

uint8 mode

bool flow_control
bool forwarding
bool mavlink_v2
bool ftp

uint8 streams

float32 data_rate                       # 配置的最大数据速率（字节/秒）

float32 rate_multiplier

float32 tx_rate_avg                     # 发送速率平均值（字节/秒）
float32 tx_error_rate_avg               # 发送错误速率平均值（字节/秒）
uint32 tx_message_count                 # 总消息发送次数
uint32 tx_buffer_overruns               # 发送缓冲区溢出次数

float32 rx_rate_avg                     # 接收速率平均值（字节/秒）
uint32 rx_message_count                 # 总消息接收次数
uint32 rx_message_lost_count
uint32 rx_buffer_overruns               # 接收缓冲区溢出次数
uint32 rx_parse_errors                  # 解析错误次数
uint32 rx_packet_drop_count             # 数据包丢弃次数
float32 rx_message_lost_rate


uint64 HEARTBEAT_TIMEOUT_US = 2500000       # 心跳超时（允许丢失1个+抖动）

# 按类型划分的心跳
bool heartbeat_type_antenna_tracker         # MAV_TYPE_ANTENNA_TRACKER
bool heartbeat_type_gcs                     # MAV_TYPE_GCS
bool heartbeat_type_onboard_controller      # MAV_TYPE_ONBOARD_CONTROLLER
bool heartbeat_type_gimbal                  # MAV_TYPE_GIMBAL
bool heartbeat_type_adsb                    # MAV_TYPE_ADSB
bool heartbeat_type_camera                  # MAV_TYPE_CAMERA
bool heartbeat_type_parachute               # MAV_TYPE_PARACHUTE
bool heartbeat_type_open_drone_id           # MAV_TYPE_ODID

# 按组件划分的心跳
bool heartbeat_component_telemetry_radio    # MAV_COMP_ID_TELEMETRY_RADIO
bool heartbeat_component_log                # MAV_COMP_ID_LOG
bool heartbeat_component_osd                # MAV_COMP_ID_OSD
bool heartbeat_component_vio                # MAV_COMP_ID_VISUAL_INERTIAL_ODOMETRY
bool heartbeat_component_pairing_manager    # MAV_COMP_ID_PAIRING_MANAGER
bool heartbeat_component_udp_bridge         # MAV_COMP_ID_UDP_BRIDGE
bool heartbeat_component_uart_bridge        # MAV_COMP_ID_UART_BRIDGE

# 其他组件状态
bool open_drone_id_system_healthy
bool parachute_system_healthy

```