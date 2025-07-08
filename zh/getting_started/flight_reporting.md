# 飞行报告

PX4 记录详细的机体状态和传感器数据，可用于分析性能问题。  
本主题解释了如何下载和分析日志，并与开发团队分享以供审查。

:::tip  
在某些司法管辖区，保留飞行日志是法律要求。  
:::

## 从飞控下载日志  

可通过 [QGroundControl](http://qgroundcontrol.com/) 下载日志：**[Analyze View > Log Download](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/log_download.html)**。

![Flight Log Download](../../assets/qgc/analyze/log_download.jpg)

::: tip  
加密日志无法通过 QGroundControl 下载，也不能上传到公共 Flight Review 服务。  
下载和提取加密日志的最简单方式是使用 [Log Encryption Tools](../dev_log/log_encryption.md)。  
您还可以部署 [private Flight Review 服务器](../dev_log/log_encryption.md#flight-review-encrypted-logs)，通过私钥在上传时自动解密日志。  
:::

## 分析日志  

将日志文件上传到在线 [Flight Review](http://logs.px4.io) 工具。  
上传完成后，您将收到一封包含分析页面链接的电子邮件。

[Log Analysis using Flight Review](../log/flight_review.md) 解释了如何解读图表，并可帮助您验证或排除常见问题的原因：过度振动、PID 参数不佳、控制器饱和、机体不平衡、GPS 噪声等。

::: info  
有许多其他优秀的工具可用于可视化和分析 PX4 日志。  
更多信息请参见：[Flight Analysis](../dev_log/flight_log_analysis.md)。  
:::

:::tip  
如果与机体保持稳定的高频率 MAVLink 连接（而不仅仅是遥测链路），则可使用 _QGroundControl_ 直接将日志上传到 _Flight Review_。  
更多信息请参见 [Settings > MAVLink Settings > MAVLink 2 Logging (PX4 only)](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/mavlink.html#logging)。  
:::

## 与 PX4 开发者共享日志文件  

可通过 [Flight Review](http://logs.px4.io) 获取的日志链接，在 [支持论坛](../contribute/support.md#forums-and-chat) 或 [Github issue](../index.md#reporting-bugs-issues) 中讨论。

## 日志配置  

日志系统默认配置为收集适用于 [Flight Review](http://logs.px4.io) 的合理日志。  

可通过 [SD Logging](../advanced_config/parameter_reference.md#sd-logging) 参数或 SD 卡上的文件进一步配置日志。  
配置详情请参见 [logging 配置文档](../dev_log/logging.md#configuration)。

## 关键链接  

- [Flight Review](http://logs.px4.io)  
- [Log Analysis using Flight Review](../log/flight_review.md)  
- [Flight Log Analysis](../dev_log/flight_log_analysis.md)