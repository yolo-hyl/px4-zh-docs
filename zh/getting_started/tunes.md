# 调音含义（Pixhawk系列）

[Pixhawk系列飞控](../flight_controller/pixhawk_series.md)使用[buzzer](../getting_started/px4_basic_concepts.md#buzzer)发出的可听音调/调音以及[LED](../getting_started/led_meanings.md)的颜色/序列来指示机体状态和事件（例如解锁成功和失败、低电量警告等）。

标准音调集合列于下方。

::: info
**开发者：** 调音定义在[/lib/tunes/tune_definition.desc](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc)中，可使用[tune-control](../modules/modules_system.md#tune-control)模块进行测试。
可通过字符串 `TUNE_ID_name`（例如 `TUNE_ID_PARACHUTE_RELEASE`）搜索调音使用情况
:::

## 启动/开机

这些音效在启动序列中播放。
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/rcS -->


#### 启动音调

<audio controls><source src="../../assets/tunes/1_startup_tone.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- tune: 1, STARTUP -->

- microSD卡已成功挂载（在启动期间）。

#### 错误音调

<audio controls><source src="../../assets/tunes/2_error_tune.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- tune 2, ERROR_TUNE -->

- 硬故障导致系统重启。
- 系统设置为使用PX4IO但未检测到IO。
- UAVCAN已启用但驱动无法启动。
- SITL/HITL已启用但*pwm_out_sim*驱动无法启动。
- FMU启动失败。


#### 制作文件系统

<audio controls><source src="../../assets/tunes/16_make_fs.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- 14, SD_INIT (previously tune 16) -->

- 正在格式化microSD卡。
- 挂载失败（如果格式化成功，启动序列将尝试重新挂载）。
- 未检测到microSD卡。


#### 格式化失败

<audio controls><source src="../../assets/tunes/17_format_failed.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- 15, SD_ERROR (previously 17) -->

- microSD卡格式化失败（在之前的挂载尝试之后）。


#### 程序烧录PX4IO

<audio controls><source src="../../assets/tunes/18_program_px4io.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- 16, PROG_PX4IO (previously id 18) -->

- 开始烧录PX4IO程序。


#### 程序烧录PX4IO成功

<audio controls><source src="../../assets/tunes/19_program_px4io_success.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- 17, PROG_PX4IO_OK (previously tune 19) -->

- PX4IO程序烧录成功。


#### 程序烧录PX4IO失败

<audio controls><source src="../../assets/tunes/20_program_px4io_fail.mp3" type="audio/mpeg">Your browser does not support the audio element.</audio>
<!-- 18, PROG_PX4IO_ERR (previously tune 20) -->

- PX4IO程序烧录失败。
- PX4IO无法启动。
- 未找到AUX Mixer。

## 操作

这些音调/旋律在正常操作期间发出。

<a id="error_tune_operational"></a>
#### 错误音调

<audio controls><source src="../../assets/tunes/2_error_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 2, ERROR_TUNE -->

- 遥控丢失

#### 通知正向音调

<audio controls><source src="../../assets/tunes/3_notify_positive_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 3, NOTIFY_POSITIVE -->

- 校准成功。
- 模式切换成功。
- 命令已接受（例如来自MAVLink命令协议）。
- 安全开关关闭（机体可启动）。

#### 通知中性音调

<audio controls><source src="../../assets/tunes/4_notify_neutral_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 4, NOTIFY_NEUTRAL -->

- 任务有效且无警告。
- 空速校准：提供更多气压，或校准完成。
- 安全开关开启/解除（可安全接近机体）。

#### 通知负向音调

<audio controls><source src="../../assets/tunes/5_notify_negative_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 5, NOTIFY_NEGATIVE -->

- 校准失败。
- 校准已完成。
- 任务无效。
- 命令被拒绝/失败/临时拒绝（例如来自MAVLink命令协议）。
- 启动/解除启动转换被拒绝（例如预飞检查失败、安全开关未关闭、系统不在手动模式）。
- 模式转换被拒绝。

#### 启动警告

<audio controls><source src="../../assets/tunes/6_arming_warning.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 6, ARMING_WARNING -->

- 机体已启动。

#### 启动失败音调

<audio controls><source src="../../assets/tunes/10_arming_failure_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 10, ARMING_FAILURE -->

- 启动失败

#### 电池警告慢速

<audio controls><source src="../../assets/tunes/7_battery_warning_slow.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 7,  BATTERY_WARNING_SLOW -->

- 低电量警告（[失效保护](../config/safety.md#battery-level-failsafe)）。

#### 电池警告快速

<audio controls><source src="../../assets/tunes/8_battery_warning_fast.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 8, BATTERY_WARNING_FAST -->

- 严重低电量警告（[失效保护](../config/safety.md#battery-level-failsafe)）。

#### GPS警告慢速

<audio controls><source src="../../assets/tunes/9_gps_warning_slow.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 9,  GPS_WARNING -->

#### 降落伞释放

<audio controls><source src="../../assets/tunes/11_parachute_release.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 11, PARACHUTE_RELEASE -->

- 降落伞释放触发。

#### 单次蜂鸣

<audio controls><source src="../../assets/tunes/14_single_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 12, SINGLE_BEEP (previously was id 14 -->

- 磁力计/指南针校准：通知用户开始旋转机体。

#### 家点设置音调

<audio controls><source src="../../assets/tunes/15_home_set_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 13, HOME_SET (previously id 15) -->

- 家点位置初始化（仅首次）。

#### 电源关闭音调

<audio controls><source src="../../assets/tunes/power_off_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>

- 机体正在关机。

<!--19, POWER_OFF -->