# 手动控制开关 (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ManualControlSwitches.msg)

```c
uint64 timestamp                 # 系统启动后经过的时间 (微秒)

uint64 timestamp_sample          # 原始数据的时间戳 (微秒)

uint8 SWITCH_POS_NONE   = 0      # 开关未映射
uint8 SWITCH_POS_ON     = 1      # 开关激活 (值=1)
uint8 SWITCH_POS_MIDDLE = 2      # 中间位置 (值=0)
uint8 SWITCH_POS_OFF    = 3      # 开关未激活 (值=-1)

uint8 MODE_SLOT_NONE    = 0      # 未分配模式槽位
uint8 MODE_SLOT_1       = 1      # 选择模式槽位1
uint8 MODE_SLOT_2       = 2      # 选择模式槽位2
uint8 MODE_SLOT_3       = 3      # 选择模式槽位3
uint8 MODE_SLOT_4       = 4      # 选择模式槽位4
uint8 MODE_SLOT_5       = 5      # 选择模式槽位5
uint8 MODE_SLOT_6       = 6      # 选择模式槽位6
uint8 MODE_SLOT_NUM     = 6      # 槽数量

uint8 mode_slot                  # 指定模式选择器所处的槽位

uint8 arm_switch                 # 解锁/上锁开关: _未解锁_, 解锁
uint8 return_switch              # 返航双位置开关 (必选): _常规_, 返航
uint8 loiter_switch              # 悬停双位置开关 (可选): _任务_, 悬停
uint8 offboard_switch            # 外部控制双位置开关 (可选): _常规_, 外部控制
uint8 kill_switch                # 油门切断: _常规_, 切断
uint8 gear_switch                # 起落架开关: _放下_, 收起
uint8 transition_switch          # 垂直起降切换开关: _悬停, 前飞

uint8 photo_switch               # 照片触发开关
uint8 video_switch               # 视频触发开关

uint8 payload_power_switch       # 载荷供电开关

uint8 engage_main_motor_switch   # 启动主电机 (用于直升机)

uint32 switch_changes            # 开关状态变化次数

```