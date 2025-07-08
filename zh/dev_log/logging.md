# 日志记录

[系统记录器](../modules/modules_system.md#logger)能够记录任何包含所有字段的ORB主题。
所有必要内容均从`.msg`文件生成，因此只需指定主题名称。
一个可选的间隔参数可指定特定主题的最大记录速率。
所有主题的现有实例均被记录。

输出日志格式为[ULog](../dev_log/ulog_file_format.md)。

[加密日志记录](../dev_log/log_encryption.md)也得到支持。

## 使用

默认情况下，上锁时会自动开始记录，解除上锁时停止记录。  
每次上锁会话都会在SD卡上创建一个新的记录文件。  
要查看当前状态，请在控制台中使用 `logger status`。  
如果希望立即开始记录，请使用 `logger on`。  
这将覆盖上锁状态，仿佛系统已上锁。  
`logger off` 会撤销此操作。  

如果因写入错误或达到[最大文件大小](#file-size-limitations)而停止记录，PX4 将自动在新文件中重新开始记录。  

要查看所有支持的记录器命令和参数列表，请使用：  

```
logger help
```

## 配置

日志系统默认配置为通过 [Flight Review](http://logs.px4.io) 收集适用于 [飞行报告](../getting_started/flight_reporting.md) 的日志。

可以通过 [SD Logging](../advanced_config/parameter_reference.md#sd-logging) 参数进一步配置日志功能。最可能需要修改的参数如下：

| 参数名                                                                 | 描述                                                                                                                                                                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [SDLOG_MODE](../advanced_config/parameter_reference.md#SDLOG_MODE)       | 日志模式。定义日志的开始和停止时间。<br />- `-1`: 禁用日志。<br />- `0`: 武装时开始日志直到解除武装（默认）。<br />- `1`: 从启动到解除武装。<br />- `2`: 从启动到关机。<br />- `3`: 基于 [AUX1 RC通道](../advanced_config/parameter_reference.md#RC_MAP_AUX1) 的日志。<br />- `4`: 从首次武装到关机。 |
| [SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE) | 日志配置文件。用于启用不太常见的日志/分析（例如 EKF2 回放、高频率日志用于 PID & 滤波器调优、热温度校准）。                                                                                                                                                                                                             |
| [SDLOG_MISSION](../advanced_config/parameter_reference.md#SDLOG_MISSION) | 创建额外的非常小的 "任务日志"。<br>该日志 _不能_ 与 [Flight Review](../log/flight_log_analysis.md#flight-review-online-tool) 一起使用，但当需要用于地理标记或法规合规的小日志时很有用。                                                                                                                                        |

特定场景的有用设置：

- 用于比较的原始传感器数据：[SDLOG_MODE=1](../advanced_config/parameter_reference.md#SDLOG_MODE) 和 [SDLOG_PROFILE=64](../advanced_config/parameter_reference.md#SDLOG_PROFILE)
- 完全禁用日志：[SDLOG_MODE=`-1`](../advanced_config/parameter_reference.md#SDLOG_MODE)

### Logger 模块

_开发者_ 可通过 [logger](../modules/modules_system.md#logger) 模块进一步配置日志内容。
这允许例如记录自定义的 uORB 话题。

### SD 卡配置

此外，日志话题列表也可以通过 SD 卡上的文件进行自定义。
在卡上创建文件 `etc/logging/logger_topics.txt` 并列出话题（SITL 中路径为 `build/px4_sitl_default/rootfs/fs/microsd/etc/logging/logger_topics.txt`）：

```plain
<topic_name> <interval> <instance>
```

`<interval>` 是可选的，如果指定则表示该话题两个日志消息之间的最小间隔（单位：ms）。
如果不指定，则以最大频率记录该话题。

`<instance>` 是可选的，如果指定则表示记录的实例。
如果不指定，则记录该话题的所有实例。
要指定 `<instance>`，必须同时指定 `<interval>`。可以设置为 0 以最大频率记录

该文件中的话题将完全替代默认的日志话题。

示例：

```plain
sensor_accel 0 0
sensor_accel 100 1
sensor_gyro 200
sensor_mag 200 1
```

此配置将以最大频率记录 sensor_accel 0，以 10Hz 记录 sensor_accel 1，以 5Hz 记录所有 sensor_gyro 实例，并以 5Hz 记录 sensor_mag 1。

## 脚本

有几个脚本用于分析和转换日志文件，这些脚本位于 [pyulog](https://github.com/PX4/pyulog) 仓库中。

## 文件大小限制

最大文件大小取决于文件系统和操作系统。NuttX上的大小限制目前约为2GB。

## 掉帧

日志掉帧是不期望出现的现象，其发生程度受以下多个因素影响：

- 我们测试的大多数SD卡每分钟都会出现多次暂停。  
  这会表现为写入命令期间出现数百毫秒的延迟。  
  如果在此期间写入缓冲区填满，则会导致掉帧。  
  该现象取决于SD卡类型（详见下文）。  
- 格式化SD卡有助于减少掉帧。  
- 增加日志缓冲区大小有助于缓解问题。  
- 降低选定主题的日志记录频率，或从日志中移除不需要的主题（`info.py <file>` 对此很有帮助）。

## SD 卡

NuttX 最大支持的 SD 卡容量为 32GB（SD Memory Card Specifications Version 2.0）。  
**SanDisk Extreme U3 32GB** 和 **Samsung EVO Plus 32** 被证实为可靠的存储卡（不会出现写入时间尖峰，因此几乎不会发生记录中断）。

下表展示了 **平均顺序写入速度 [KB/s]** / **每块（4KB）最大写入时间（平均值）[ms]**，适用于基于 F4-（Pixracer）、F7- 和 H7 的飞控。

| SD 卡                                                       | F4            | F7         | H7        |
| ------------------------------------------------------------- | ------------- | ---------- | --------- |
| SanDisk Extreme U3 32GB                                       | 1500 / **15** | 1800/10    | 2900/8    |
| Samsung EVO Plus 32GB                                         | 1700/10-60    | 1800/10-60 | 1900/9-60 |
| Sandisk Ultra Class 10 8GB                                    | 348 / 40      | ?/?        | ?/?       |
| Sandisk Class 4 8GB                                           | 212 / 60      | ?/?        | ?/?       |
| SanDisk Class 10 32 GB (High Endurance Video Monitoring Card) | 331 / 220     | ?/?        | ?/?       |
| Lexar U1 (Class 10), 16GB High-Performance                    | 209 / 150     | ?/?        | ?/?       |
| Sandisk Ultra PLUS Class 10 16GB                              | 196 /500      | ?/?        | ?/?       |
| Sandisk Pixtor Class 10 16GB                                  | 334 / 250     | ?/?        | ?/?       |
| Sandisk Extreme PLUS Class 10 32GB                            | 332 / 150     | ?/?        | ?/?       |

使用默认话题时，日志带宽约为 50 KB/s，几乎所有 SD 卡的平均顺序写入速度都能满足要求。

比平均写入速度更重要的是最大写入时间（每 4KB 块）或 `fsync` 时间的尖峰（或高值），因为较长的写入时间意味着需要更大的日志缓冲区才能避免记录中断。

PX4 在 F7/H7 上使用了更大的缓冲区和读取缓存，这足以补偿许多低性能存储卡的尖峰问题。  
尽管如此，如果存储卡的 `fsync` 或写入时间达到数百毫秒级，则不建议用于 PX4。  
您可以通过运行 [sd_bench](../modules/modules_command.md#sd-bench) 并增加迭代次数（约 100 次）来检测该值。

```sh
sd_bench -r 100
```

这定义了最小缓冲区大小：最大值越大，为了避免记录中断所需的日志缓冲区也越大。  
PX4 在 F7/H7 上使用了更大的缓冲区和读取缓存来弥补这些问题。

::: info
如果您对某张存储卡有疑虑，可以运行上述测试并将结果提交到 https://github.com/PX4/PX4-Autopilot/issues/4634。
:::

## 日志流

传统的且仍然完全支持的记录方式是使用FMU上的SD卡进行日志记录。  
但存在另一种替代方案：日志流，它通过MAVLink传输相同的日志数据。  
该方法可用于FMU没有SD卡插槽的情况（例如Intel® Aero Ready to Fly无人机），或单纯为了避免使用SD卡。  
两种方法可以独立使用，也可同时使用。

要求链路至少提供约50KB/s的带宽，例如WiFi链路。  
同时只能有一个客户端请求日志流。  
连接不需要可靠，协议设计可处理数据包丢失。

支持ulog流的不同客户端包括：

- PX4-Autopilot/Tools中的`mavlink_ulog_streaming.py`脚本  
- QGroundControl:  
  ![QGC日志流](../../assets/gcs/qgc-log-streaming.png)  
- [MAVGCL](https://github.com/ecmnet/MAVGCL)

### 诊断

- 如果日志流未启动，请确保`logger`正在运行（见上文），并在启动时检查控制台输出。  
- 如果仍然无效，请确保使用MAVLink 2。  
  通过将`MAV_PROTO_VER`设置为2来强制启用。  
- 日志流最多使用配置的MAVLink速率的70%（`-r`参数）。  
  如果需要更多，消息将被丢弃。  
  当前使用百分比可通过`mavlink status`查看（示例中使用1.8%）：

  ```sh
  instance #0:
          GCS heartbeat:  160955 us ago
          mavlink chan: #0
          type:           GENERIC LINK OR RADIO
          flow control:   OFF
          rates:
          tx: 95.781 kB/s
          txerr: 0.000 kB/s
          rx: 0.021 kB/s
          rate mult: 1.000
          ULog rate: 1.8% of max 70.0%
          accepting commands: YES
          MAVLink version: 2
          transport protocol: UDP (14556)
  ```

  同时确保`txerr`保持为0。  
  如果该值上升，可能是NuttX发送缓冲区过小、物理链路过载或硬件处理数据速度过慢。

## 另请参阅

- [加密日志记录](../dev_log/log_encryption.md)