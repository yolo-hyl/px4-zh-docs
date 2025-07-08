# LedControl (UORB消息)

LED控制：控制单个或多个LED。  
这些是外部可见的LED，不包括板载LED  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/LedControl.msg)

```c
# LED控制：控制单个或多个LED。
# 这些是外部可见的LED，不包括板载LED

uint64 timestamp		# 系统启动后的时间（微秒）

# 颜色定义
uint8 COLOR_OFF = 0 # 仅在驱动中使用
uint8 COLOR_RED = 1
uint8 COLOR_GREEN = 2
uint8 COLOR_BLUE = 3
uint8 COLOR_YELLOW = 4
uint8 COLOR_PURPLE = 5
uint8 COLOR_AMBER = 6
uint8 COLOR_CYAN = 7
uint8 COLOR_WHITE = 8

# LED模式定义
uint8 MODE_OFF = 0 # 关闭LED
uint8 MODE_ON = 1  # 打开LED
uint8 MODE_DISABLED = 2  # 禁用该优先级（切换到较低优先级设置）
uint8 MODE_BLINK_SLOW = 3
uint8 MODE_BLINK_NORMAL = 4
uint8 MODE_BLINK_FAST = 5
uint8 MODE_BREATHE = 6 # 持续增加与减少亮度（如果驱动不支持则显示纯色）
uint8 MODE_FLASH = 7 # 快速闪烁两次（开/关），时间与MODE_BLINK_FAST相同，之后保持关闭一段时间

uint8 MAX_PRIORITY = 2 # 最大优先级（最小值为0）

uint8 led_mask # 位掩码，指定控制哪个LED（0xff表示全部）
uint8 color # 参见COLOR_*
uint8 mode # 参见MODE_*
uint8 num_blinks # 闪烁次数（如果是MODE_BLINK_*模式则表示开/关周期数）。设为0表示无限次
                 # 在MODE_FLASH中表示周期数。最大闪烁次数：122，最大闪烁周期数：20
uint8 priority # 优先级：高优先级事件会覆盖当前低优先级事件（参见MAX_PRIORITY）

uint8 ORB_QUEUE_LENGTH = 8      # 需与BOARD_MAX_LEDS匹配
```