# 调音含义（Pixhawk 系列）

[Pixhawk系列飞控](../flight_controller/pixhawk_series.md)使用来自[buzzer](../getting_started/px4_basic_concepts.md#buzzer)的可听音调和来自[LED](../getting_started/led_meanings.md)的颜色/序列来指示机体状态和事件（例如武装成功与失败、低电量警告）。

标准音调集合如下所示。

::: info
**开发者：** 音调定义在[/lib/tunes/tune_definition.desc](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc)中，可以通过[tune-control](../modules/modules_system.md#tune-control)模块进行测试。
您可以使用字符串 `TUNE_ID_name`（例如 `TUNE_ID_PARACHUTE_RELEASE`）搜索音调使用情况。
:::

## 启动/启动

这些音调在启动序列期间播放。
<!-- https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d/rcS --> 

#### 启动音调

<audio controls><source src="../../assets/tunes/1_startup_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- tune: 1, STARTUP -->

- microSD卡成功挂载（在启动期间）。

#### 错误音调

<audio controls><source src="../../assets/tunes/2_error_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- tune 2, ERROR_TUNE -->

- 严重错误导致系统重启。
- 系统设置为使用PX4IO但未检测到IO。
- UAVCAN已启用但驱动无法启动。
- SITL/HITL已启用但*pwm_out_sim*驱动无法启动。
- FMU启动失败。

#### 创建文件系统

<audio controls><source src="../../assets/tunes/16_make_fs.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 14, SD_INIT (previously tune 16) -->

- 格式化microSD卡。
- 挂载失败（如果格式化成功，启动序列将再次尝试挂载）。
- 没有microSD卡。

#### 格式化失败

<audio controls><source src="../../assets/tunes/17_format_failed.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 15, SD_ERROR (previously 17) -->

- 格式化microSD卡失败（在之前尝试挂载卡之后）。

#### 编程PX4IO

<audio controls><source src="../../assets/tunes/18_program_px4io.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 16, PROG_PX4IO (previously id 18) -->

- 开始编程PX4IO。

#### 编程PX4IO成功

<audio controls><source src="../../assets/tunes/19_program_px4io_success.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 17, PROG_PX4IO_OK (previously tune 19) -->

- PX4IO编程成功。

#### 编程PX4IO失败

<audio controls><source src="../../assets/tunes/20_program_px4io_fail.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 18, PROG_PX4IO_ERR (previously tune 20) -->

- PX4IO编程失败。
- PX4IO无法启动。
- 未找到AUX Mixer。

## 操作过程

这些音调/警报在正常操作期间发出。

<a id="error_tune_operational"></a>
#### 错误音调

<audio controls><source src="../../assets/tunes/2_error_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 2, ERROR_TUNE -->

- 遥控丢失

#### 正面通知音调

<audio controls><source src="../../assets/tunes/3_notify_positive_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 3, NOTIFY_POSITIVE -->

- 校准成功。
- 模式切换成功。
- 命令已接受（例如来自MAVLink命令协议）。
- 安全开关关闭（机体可以武装）。

#### 中性通知音调

<audio controls><source src="../../assets/tunes/4_notify_neutral_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 4, NOTIFY_NEUTRAL -->

- 任务有效且无警告。
- 空速校准：提供更多气压，或校准完成。
- 安全开关已开启/解除武装（接近机体安全）。

#### 负面通知音调

<audio controls><source src="../../assets/tunes/5_notify_negative_tone.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 5, NOTIFY_NEGATIVE -->

- 校准失败。
- 校准已完成。
- 任务无效。
- 命令被拒绝、失败、暂时拒绝（例如来自MAVLink命令协议）。
- 武装/解除武装转换被拒绝（例如预飞行检查失败、安全未禁用、系统不在手动模式）。
- 拒绝模式转换。

#### 武装警告

<audio controls><source src="../../assets/tunes/6_arming_warning.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 6, ARMING_WARNING -->

- 机体已武装。

#### 武装失败音调

<audio controls><source src="../../assets/tunes/10_arming_failure_tune.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 10, ARMING_FAILURE -->

- 武装失败

#### 电池警告慢速

<audio controls><source src="../../assets/tunes/7_battery_warning_slow.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 7,  BATTERY_WARNING_SLOW -->

- 低电量警告（[failsafe](../config/safety.md#battery-level-failsafe)）。

#### 电池警告快速

<audio controls><source src="../../assets/tunes/8_battery_warning_fast.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 8, BATTERY_WARNING_FAST -->

- 严重低电量警告（[failsafe](../config/safety.md#battery-level-failsafe)）。

#### GPS警告慢速

<audio controls><source src="../../assets/tunes/9_gps_warning_slow.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 9,  GPS_WARNING -->

#### 降落伞释放

<audio controls><source src="../../assets/tunes/11_parachute_release.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 11, PARACHUTE_RELEASE -->

- 降落伞已释放

#### 单次音调

<audio controls><source src="../../assets/tunes/1_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 12, SINGLE_BEEP -->

- 操作确认音调

#### 磁罗盘校准

<audio controls><source src="../../assets/tunes/compass_calibrate.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 13, COMPASS_CALIBRATE -->

- 磁罗盘校准提示音

#### 独立音调

<audio controls><source src="../../assets/tunes/standalone_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 14, STANDALONE_BEEP -->

- 独立操作音调

#### 低电量警告

<audio controls><source src="../../assets/tunes/low_battery.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 15, LOW_BATTERY -->

- 电池电量即将耗尽

#### 单次蜂鸣音

<audio controls><source src="../../assets/tunes/one_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 16, ONE_BEEP -->

- 简单确认音调

#### 长蜂鸣音

<audio controls><source src="../../assets/tunes/long_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 17, LONG_BEEP -->

- 持续警报音调

#### 三短音

<audio controls><source src="../../assets/tunes/three_short_beeps.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 18, THREE_SHORT_BEEPS -->

- 快速连续警报

#### 两短一长音

<audio controls><source src="../../assets/tunes/two_short_one_long_beep.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 19, TWO_SHORT_ONE_LONG_BEEP -->

- 混合警报模式

#### 一长两短音

<audio controls><source src="../../assets/tunes/one_long_two_short_beeps.mp3" type="audio/mpeg">您的浏览器不支持音频元素。</audio>
<!-- 20, ONE_LONG_TWO_SHORT_BEEPS -->

- 复杂警报序列