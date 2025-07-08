# TuneControl (UORB消息)

该消息用于控制音调，当 tune_id 设置为 CUSTOM 时，会使用 frequency 和 duration 参数，否则这些值将被忽略。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TuneControl.msg)

```c
# 该消息用于控制音调，当 tune_id 设置为 CUSTOM 时，会使用 frequency 和 duration 参数，否则这些值将被忽略。

uint64 timestamp     # 系统启动后时间戳（微秒）

uint8 TUNE_ID_STOP                 = 0
uint8 TUNE_ID_STARTUP              = 1
uint8 TUNE_ID_ERROR                = 2
uint8 TUNE_ID_NOTIFY_POSITIVE      = 3
uint8 TUNE_ID_NOTIFY_NEUTRAL       = 4
uint8 TUNE_ID_NOTIFY_NEGATIVE      = 5
uint8 TUNE_ID_ARMING_WARNING       = 6
uint8 TUNE_ID_BATTERY_WARNING_SLOW = 7
uint8 TUNE_ID_BATTERY_WARNING_FAST = 8
uint8 TUNE_ID_GPS_WARNING          = 9
uint8 TUNE_ID_ARMING_FAILURE       = 10
uint8 TUNE_ID_PARACHUTE_RELEASE    = 11
uint8 TUNE_ID_SINGLE_BEEP          = 12
uint8 TUNE_ID_HOME_SET             = 13
uint8 TUNE_ID_SD_INIT              = 14
uint8 TUNE_ID_SD_ERROR             = 15
uint8 TUNE_ID_PROG_PX4IO           = 16
uint8 TUNE_ID_PROG_PX4IO_OK        = 17
uint8 TUNE_ID_PROG_PX4IO_ERR       = 18
uint8 TUNE_ID_POWER_OFF            = 19
uint8 NUMBER_OF_TUNES              = 20

uint8 tune_id        # 对应 tunes 库中 tune_defaults.h 中定义的 TuneID::* 音调ID
bool tune_override   # 如果为 true，正在播放的音调将停止并开始播放新音调
uint16 frequency     # 赫兹
uint32 duration      # 微秒
uint32 silence       # 微秒
uint8 volume         # 范围 0-100（取决于后端支持）

uint8 VOLUME_LEVEL_MIN = 0
uint8 VOLUME_LEVEL_DEFAULT = 20
uint8 VOLUME_LEVEL_MAX = 100

uint8 ORB_QUEUE_LENGTH = 4
```