# PX4 SD卡布局

PX4 SD卡用于存储配置文件、飞行日志、任务信息等。

:::tip
SD卡应使用FAT32格式用于PX4（这是SD卡的默认格式）。我们建议重新格式化使用其他文件系统的存储卡。
:::

目录结构/布局如下所示。

| 目录/文件(s)           | 描述                                                                                   |
| ---------------------- | -------------------------------------------------------------------------------------- |
| `/etc/`                | 额外配置。参见 [系统启动 > 替换系统启动][replace system start]。                       |
| `/log/`                | 完整[飞行日志](../dev_log/logging.md)                                                  |
| `/mission_log/`        | 简化飞行日志                                                                           |
| `/fw/`                 | [DroneCAN](../dronecan/index.md) 固件                                                 |
| `/uavcan.db/`          | DroneCAN DNA服务器数据库 + 日志                                                         |
| `/params`              | 参数（如果不在FRAM/FLASH中）                                                           |
| `/dataman`             | 任务存储文件                                                                           |
| `/fault_<datetime>.txt` | 硬故障文件                                                                             |
| `/bootlog.txt`         | 启动日志文件                                                                           |

[replace system start]: ../concept/system_startup.md#replacing-the-system-startup