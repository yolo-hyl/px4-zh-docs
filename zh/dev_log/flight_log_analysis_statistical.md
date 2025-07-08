# 统计飞行日志分析

本主题包含与统计飞行日志分析相关的信息和资源。

## 飞行日志分析公共日志

[Flight Review](../log/flight_log_analysis.md#flight-review-online-tool) 托管了大量公开可用的日志文件，可用于统计分析、机器学习或其他用途。

数据集包含以下不同类别：

- 机体类型  
- PX4 版本（包括开发版本）  
- 主板  
- 飞行模式  

日志文件可通过 [logs.px4.io/browse](https://logs.px4.io/browse) 访问，并以 [CC-BY PX4](https://creativecommons.org/licenses/by/4.0/) 许可证发布。

日志文件也可通过 [download_logs.py](https://github.com/PX4/flight_review/blob/main/app/download_logs.py) 脚本批量下载。  
该脚本支持按不同属性（如飞行模式、机体名称或类型）进行过滤。  
使用 `--help` 参数可查看完整列表。  
最新日志将优先下载，下载过程可中断并稍后恢复。

有多种解析库可供使用，例如 [pyulog](../log/flight_log_analysis.md#pyulog) 可用于通过 Python 读取日志。