# 模块参考：Ins（驱动）

## vectornav

源代码路径: [drivers/ins/vectornav](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/ins/vectornav)


### 描述

VectorNav VN-100、VN-200、VN-300的串行总线驱动程序。

大多数开发板通过SENS_VN_CFG参数配置在指定UART上启用/启动驱动程序。

设置/使用说明: https://docs.px4.io/main/en/sensor/vectornav.html

### 示例

尝试在指定串行设备上启动驱动程序。
```
vectornav start -d /dev/ttyS1
```
停止驱动程序
```
vectornav stop
```

### 用法 {#vectornav_usage}

```
vectornav <命令> [参数...]
 命令:
   start         启动驱动程序
     -d <val>    串行设备

   status        驱动程序状态

   stop          停止驱动程序

   status        打印驱动程序状态
```