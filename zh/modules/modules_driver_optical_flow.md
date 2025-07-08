# 模块参考：光流（驱动程序）

## thoneflow

源码位置：[drivers/optical_flow/thoneflow](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/optical_flow/thoneflow)

### 描述

ThoneFlow-3901U 光流传感器的串行总线驱动程序。

大多数开发板通过SENS_TFLOW_CFG参数配置以在指定UART上启用/启动驱动程序。

设置/使用信息：https://docs.px4.io/main/en/sensor/pmw3901.html#thone-thoneflow-3901u

### 示例

尝试在指定的串行设备上启动驱动程序。
```
thoneflow start -d /dev/ttyS1
```
停止驱动程序
```
thoneflow stop
```

### 用法 {#thoneflow_usage}

```
thoneflow <命令> [参数...]
 命令：
   start         启动驱动程序
     -d <val>    串行设备

   stop          停止驱动程序

   info          打印驱动程序信息
```