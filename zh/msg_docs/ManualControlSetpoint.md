# 手动控制设定点 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ManualControlSetpoint.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp                        # 系统启动后时间戳 (微秒)
uint64 timestamp_sample                 # 原始数据时间戳 (微秒)

bool valid

uint8 SOURCE_UNKNOWN   = 0
uint8 SOURCE_RC        = 1		# 无线电控制 (input_rc)
uint8 SOURCE_MAVLINK_0 = 2		# mavlink 实例 0
uint8 SOURCE_MAVLINK_1 = 3		# mavlink 实例 1
uint8 SOURCE_MAVLINK_2 = 4		# mavlink 实例 2
uint8 SOURCE_MAVLINK_3 = 5		# mavlink 实例 3
uint8 SOURCE_MAVLINK_4 = 6		# mavlink 实例 4
uint8 SOURCE_MAVLINK_5 = 7		# mavlink 实例 5

uint8 data_source

# 任何通道可能不可用并设置为 NaN 以表示数据无效

# 摇杆位置 [-1,1]
# 在标准 RC 模式 1/2/3/4 遥控器/摇杆上：-1 表示向下/向左，1 表示向上/向右
# 注意：QGC 发送的油门/z 范围为 [0,1000] - [0,1]。MAVLink 输入转换 [0,1] 到 [-1,1] 目前保持向后兼容性。
# 正值通常用于：
float32 roll     # 向右移动，正横滚，右侧向下
float32 pitch    # 向前移动，负俯仰，机头向下
float32 yaw      # 正偏航，俯视时顺时针旋转
float32 throttle # 向上移动，正推力，-1 是最小可用 0% 或 -100%，+1 是 100% 推力

float32 flaps			 # 襟翼开关/旋钮/杆的位置 [-1, 1]

float32 aux1
float32 aux2
float32 aux3
float32 aux4
float32 aux5
float32 aux6

bool sticks_moving

uint16 buttons		# 来自 Mavlink manual_control 消息的 uint16 按钮字段

# TOPICS manual_control_setpoint manual_control_input
# DEPRECATED: float32 x
# DEPRECATED: float32 y
# DEPRECATED: float32 z
# DEPRECATED: float32 r

```