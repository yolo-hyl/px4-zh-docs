# 飞行日志分析

本节概述可用于分析PX4飞行日志的工具和方法（某些情况下下方链接包含更详细的主题）。

::: info INFO
[飞行报告](../getting_started/flight_reporting.md) 说明如何下载日志并与开发团队报告/讨论飞行中的问题。
:::

## 结构化分析

在分析飞行日志前，需要先建立其背景信息：

- 如果分析是在故障后进行，日志是否记录了坠毁过程，还是在空中中断了记录？
- 控制器是否成功跟踪了目标值？
  最简单的方法是将姿态滚转和俯仰速率与目标值进行对比。
- 传感器数据看起来是否有效？是否存在强烈振动（强烈振动的合理阈值是峰峰值超过2-3 m/s/s）。
- 如果根本原因不特定于机体型号，请确保在[PX4 issue tracker](https://github.com/PX4/PX4-Autopilot/issues/new)报告时附上日志文件链接（及视频文件，如果存在）。

## 排除电源故障

如果日志文件在空中结束，可能的两个主要原因：电源故障或操作系统的硬故障。

对于基于[STM32系列](http://www.st.com/en/microcontrollers/stm32-32-bit-arm-cortex-mcus.html)的自动驾驶仪，硬故障会被记录到SD卡中。  
这些文件位于SD卡顶层，文件名格式为**fault_date.log**，例如 **fault_2017_04_03_00_26_05.log**。  
如果飞行日志突然结束，请检查该文件是否存在。

## 分析工具

### 飞行日志分析工具 (在线工具)

[飞行日志分析工具](http://logs.px4.io) 是 _Log Muncher_ 的继任者。  
它与新的 [ULog](../dev_log/ulog_file_format.md) 日志格式配合使用。

主要特性：

- 基于网页，非常适合终端用户。  
- 用户可通过网页界面上传日志，并与他人分享报告（支持批量上传，使用 [upload_log.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/upload_log.py) 脚本）  
- 交互式图表。

![飞行日志分析图表](../../assets/flight_log_analysis/flight_review/flight-review-example.png)

详见 [使用飞行日志分析工具进行日志分析](../log/flight_review.md) 获取入门指南。

### PlotJuggler

[PlotJuggler](https://github.com/facontidavide/PlotJuggler) 是一款桌面应用程序，允许用户轻松可视化和分析以时间序列形式表达的数据。  
这是最佳的 ULog 分析工具之一，因为它会暴露日志中的所有信息（相比之下，[Flight Review](#flight-review-online-tool) 仅显示了一小部分数据）。  

自 2.1.4 版本起支持 ULog 文件(.ulg)。  

核心功能：  

- 直观的拖放式界面。  
- 可将数据排列在多个图表、标签页或窗口中。  
- 所有 uORB 主题都会显示并可进行图形化。  
- 排列好数据后，可将其保存为 "Layout" 文件并多次重新加载。  
- 在 _PlotJuggler_ 内部处理数据，使用自定义的 "数据转换" 功能。  

源代码和下载可在 [Github](https://github.com/facontidavide/PlotJuggler) 上获取。  

![PlotJuggler](../../assets/flight_log_analysis/plot_juggler/plotjuggler_example_view.png)  

参见 [Log Analysis using Plot Juggler](../log/plotjuggler_log_analysis.md) 以了解入门介绍。

### pyulog

[pyulog](https://github.com/PX4/pyulog) 是一个用于解析 ULog 文件的 Python 包，附带一组命令行脚本，用于提取/显示 ULog 信息并将其转换为其他文件格式。

关键功能：

- 解析 ULog 文件的 Python 库。被许多其他 ULog 分析和可视化工具用作基础库。
- 提取/显示 ULog 信息的脚本：
  - _ulog_info_：显示来自 ULog 文件的信息。
  - _ulog_messages_：显示 ULog 文件中记录的消息。
  - _ulog_params_：从 ULog 文件中提取参数。
- 将 ULog 文件转换为其他格式的脚本：
  - _ulog2csv_：将 ULog 转换为 (多个) CSV 文件。
  - _ulog2kml_：将 ULog 转换为 (多个) KML 文件。

所有脚本均安装为系统级应用程序（即它们可以在命令行中调用 - 前提是已安装 Python），并支持 `-h` 标志以获取使用说明。例如：

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

以下我们使用 _ulog_info_ 从示例文件中导出的信息。

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

### 飞行日志分析工具

[FlightPlot](https://github.com/PX4/FlightPlot) 是一款基于桌面的日志分析工具。可以从 [FlightPlot 下载页面](https://github.com/PX4/FlightPlot/releases) 下载（支持 Linux、MacOS 和 Windows 系统）。

主要功能：

- 基于 Java，跨平台运行
- 直观的图形用户界面，无需编程知识
- 支持新旧两种 PX4 日志格式（.ulg, .px4log, .bin）
- 允许将图表保存为图像

![飞行日志分析图表](../../assets/flight_log_analysis/flightplot_0.2.16.png)

### PX4Tools

[PX4Tools](https://github.com/dronecrew/px4tools) 是一个基于 Python 编写的 PX4 自动驾驶仪日志分析工具箱。推荐的安装方法是使用 [anaconda3](https://conda.io/docs/index.html)。详情请参见 [px4tools github page](https://github.com/dronecrew/px4tools)。

关键特性：

- 易于共享，用户可在 Github 上查看笔记本文件（例如 [15-09-30 Kabir Log.ipynb](https://github.com/jgoppert/lpe-analysis/blob/master/15-09-30%20Kabir%20Log.ipynb)）
- 基于 Python，跨平台，兼容 anaconda 2 和 anaconda3
- iPython/Jupyter 笔记本可用于轻松共享分析
- 高级绘图功能以支持详细分析

![PX4Tools-based analysis](../../assets/flight_log_analysis/px4tools.png)

### MAVGCL

[MAVGCL](https://github.com/ecmnet/MAVGCL) 是一个用于 PX4 的飞行中日志分析工具  
它也可以使用下载的 uLog 文件以离线模式运行  

主要功能：

- 基于 MAVLink 消息或通过 MAVLink 的 ULOG 数据的实时数据采集（50ms 采样，100ms 滚动显示）
- 时间图表通过消息（MAVLink 和 ULog）和参数变化（仅 MAVLink）进行标注
- 所选关键指标的 XY 分析
- 3D 视图（机体和观察者视角）
- MAVLink 检查器（报告原始 MAVLink 消息）
- 离线模式：从 PX4Log/ULog 导入关键指标（文件或通过 WiFi 从设备获取最新日志）
- 基于 Java 开发。已知可在 macOS 和 Ubuntu 上运行
- 还有更多 ...

![MAVGCL](../../assets/flight_log_analysis/mavgcl/time_series.png)

### 数据彗星

[数据彗星](https://github.com/dsaffo/DataComets) 是一个交互式 PX4 飞行日志分析工具，允许您将飞行数据编码到飞行路径上，按时间过滤和刷选数据，以及更多功能！

您可以使用在线版本分析小尺寸日志文件（<32Mb），或在本地运行该工具以分析更长的飞行记录。

![Data Comets](../../assets/flight_log_analysis/data_comets/data_comets_overview.gif)