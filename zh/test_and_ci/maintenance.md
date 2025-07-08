# 维护说明

本节选取并描述了一些工具，用于分析代码库的状态并支持其维护。

## 分析代码变动率

文件的变动率（即更改次数）可以作为判断哪些文件/模块可能需要重构的指标。

要获取变动率指标，可以使用类似 [Churn](https://github.com/danmayer/churn) 的工具：

```sh
gem install churn
```

以 `v1.6.0-rc2` 为例的输出结果如下：

```sh
cd src/PX4-Autopilot
churn --start_date "6 months ago"
**********************************************************************
* Revision Changes
**********************************************************************
Files
+------------------------------------------+
| file                                     |
+------------------------------------------+
| src/modules/navigator/mission.cpp        |
| src/modules/navigator/navigator_main.cpp |
| src/modules/navigator/rtl.cpp            |
+------------------------------------------+



**********************************************************************
* Project Churn
**********************************************************************

Files
+---------------------------------------------------------------------------+---------------+
| file_path                                                                 | times_changed |
+---------------------------------------------------------------------------+---------------+
| src/modules/mc_pos_control/mc_pos_control_main.cpp                        | 107           |
| src/modules/commander/commander.cpp                                       | 67            |
| ROMFS/px4fmu_common/init.d/rcS                                            | 52            |
| Makefile                                                                  | 49            |
| src/drivers/px4fmu/fmu.cpp                                                | 47            |
| ROMFS/px4fmu_common/init.d/rc.sensors                                     | 40            |
| src/drivers/boards/aerofc-v1/board_config.h                               | 31            |
| src/modules/logger/logger.cpp                                             | 29            |
| src/modules/navigator/navigator_main.cpp                                  | 28            |
```