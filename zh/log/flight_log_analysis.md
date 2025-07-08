# 飞行日志分析

本主题概述了可用于分析PX4飞行日志的工具和方法（某些情况下下方链接了更详细的主题）。

::: info INFO
[Flight Reporting](../getting_started/flight_reporting.md) 说明了如何下载日志并与开发团队报告/讨论飞行相关问题。
:::## 结构化分析

在分析飞行日志之前，需要明确其上下文：

- 如果分析是在故障后进行的，日志是否记录了坠毁过程，还是在空中突然停止？
- 控制器是否都跟踪了各自的参考值？
  最简单的方法是将姿态滚转和俯仰速率与设定值进行比较。
- 传感器数据是否正常？是否存在强烈的振动（强烈振动的合理阈值是峰峰值超过2-3 m/s²）？
- 如果根本原因不特定于机体，请通过[PX4问题追踪器](https://github.com/PX4/PX4-Autopilot/issues/new)提交包含日志文件（及视频文件，如果有的话）的链接进行报告。## 排除电源故障

如果日志文件在飞行中突然终止，可能的两个主要原因：电源故障 _或_ 操作系统的严重故障。

在基于[STM32系列](http://www.st.com/en/microcontrollers/stm32-32-bit-arm-cortex-mcus.html)的自动驾驶仪上，严重故障会被记录到SD卡。
这些文件位于SD卡的顶层目录，命名为 _fault_date.log_，例如 **fault_2017_04_03_00_26_05.log**。
如果飞行日志突然结束，应检查是否存在此类文件。## 分析工具

### Flight Review (在线工具)

[Flight Review](http://logs.px4.io) 是 _Log Muncher_ 的继任者。
它与新的 [ULog](../dev_log/ulog_file_format.md) 日志格式结合使用。

主要特点：

- 基于网页，非常适合终端用户。
- 用户可通过网页界面上传日志，并与他人共享报告（使用 [upload_log.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/upload_log.py) 脚本支持批量上传）
- 交互式图表。

![Flight Review 图表](../../assets/flight_log_analysis/flight_review/flight-review-example.png)

详见 [使用 Flight Review 进行日志分析](../log/flight_review.md) 了解简介。

### PlotJuggler

[PlotJuggler](https://github.com/facontidavide/PlotJuggler) 是一款桌面应用程序，允许用户轻松可视化和分析以时间序列形式表达的数据。
这是最佳的 ULog 分析工具之一，因为它会显示日志中的所有信息（相比之下，[Flight Review](#flight-review-online-tool) 仅显示数据的一小部分）。

它从 2.1.4 版本开始支持 ULog 文件 (.ulg)。

主要特点：

- 直观的拖放界面。
- 可将数据排列在多个图表、标签页或窗口中。
- 显示所有 uORB 话题并可生成图表。
- 一旦整理好数据，可以将其保存为 "Layout" 文件并多次重新加载。
- 在 _PlotJuggler_ 内部使用自定义 "数据转换" 来处理数据。

源代码和下载可在 [Github](https://github.com/facontidavide/PlotJuggler) 获取。

![PlotJuggler](../../assets/flight_log_analysis/plot_juggler/plotjuggler_example_view.png)

详见 [使用 Plot Juggler 进行日志分析](../log/plotjuggler_log_analysis.md) 了解简介。

### pyulog

[pyulog](https://github.com/PX4/pyulog) 是一个用于解析 ULog 文件的 Python 包，以及一组命令行脚本来提取/显示 ULog 信息并将其转换为其他文件格式。

主要特点：

- 用于解析 ULog 文件的 Python 库。许多其他 ULog 分析和可视化工具的基础库。
- 用于提取/显示 ULog 信息的脚本：
  - _ulog_info_: 显示来自 ULog 文件的信息。
  - _ulog_messages_: 显示 ULog 文件中的记录消息。
  - _ulog_params_: 从 ULog 文件中提取参数。
- 用于将 ULog 文件转换为其他格式的脚本：
  - _ulog2csv_: 将 ULog 转换为（多个）CSV 文件。
  - _ulog2kml_: 将 ULog 转换为（多个）KML 文件。

所有脚本均安装为系统级应用程序（即可以在命令行中调用 - 前提是已安装 Python），并支持 `-h` 标志获取用法说明。例如：

```sh
$ ulog_info -h
usage: ulog_info [-h] [-v] file.ulg

Display information from an ULog file

positional arguments:
  file.ulg       ULog input file

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  Verbose output
```

以下是使用 _ulog_info_ 从示例文件中导出的信息示例。

```sh
$ ulog_info sample.ulg
Logging start time: 0:01:52, duration: 0:01:08
Dropouts: count: 4, total duration: 0.1 s, max: 62 ms, mean: 29 ms
Info Messages:
 sys_name: PX4
 time_ref_utc: 0
 ver_hw: AUAV_X21
 ver_sw: fd483321a5cf50ead91164356d15aa474643aa73

Name (multi id, message size in bytes)    number of data points, total bytes
 actuator_controls_0 (0, 48)                 3269     156912
 actuator_outputs (0, 76)                    1311      99636
 commander_state (0, 9)                       678       6102
 control_state (0, 122)                      3268     398696
 cpuload (0, 16)                               69       1104
 ekf2_innovations (0, 140)                   3271     457940
 estimator_status (0, 309)                   1311     405099
 sensor_combined (0, 72)                    17070    1229040
 sensor_preflight (0, 16)                   17072     273152
 telemetry_status (0, 36)                      70       2520
 vehicle_attitude (0, 36)                    6461     232596
 vehicle_attitude_setpoint (0, 55)           3272     179960
 vehicle_local_position (0, 123)              678      83394
 vehicle_rates_setpoint (0, 24)              6448     154752
 vehicle_status (0, 45)                       294      13230
```

### FlightPlot

[FlightPlot](https://github.com/PX4/FlightPlot) 是一款基于桌面的日志分析工具。可以从 [FlightPlot 下载](https://github.com/PX4/FlightPlot/releases) 获取（Linux、MacOS、Windows）。

主要特点：

- 基于 Java，跨平台。
- 直观的图形用户界面，无需编程知识。
- 支持新旧 PX4 日志格式（.ulg、.px4log、.bin）
- 允许将图表保存为图像。

![FlightPlot](../../assets/flight_log_analysis/flight_plot/flight_plot_screenshot.png)

### PX4Tools

[PX4Tools](https://github.com/PX4/PX4Tools) 是一个交互式 PX4 飞行日志分析工具，允许您将飞行数据编码到飞行路径上，按时间过滤和刷选数据 - 还有更多功能！

您可以使用工具的在线版本分析小型日志文件（< 32Mb），或在本地运行以分析更长的飞行。

![PX4Tools](../../assets/flight_log_analysis/px4tools/px4tools_overview.gif)