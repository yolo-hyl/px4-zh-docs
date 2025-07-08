# 模块参考：相机（驱动）

## camera_trigger

源代码：[drivers/camera_trigger](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_trigger)


### 描述

相机触发驱动。

该模块触发连接到飞控输出的相机，或实现MAVLink触发协议的简单MAVLink相机。

驱动程序响应以下MAVLink触发命令（在任务中找到或通过MAVLink接收）：

- `MAV_CMD_DO_TRIGGER_CONTROL`
- `MAV_CMD_DO_DIGICAM_CONTROL`
- `MAV_CMD_DO_SET_CAM_TRIGG_DIST`
- `MAV_CMD_OBLIQUE_SURVEY`

这些命令根据时间或距离触发相机图像捕捉。每次触发图像捕捉时，会发出`CAMERA_TRIGGER` MAVLink消息。

"simple MAVLink camera"是指支持上述命令集的相机。当为此类相机配置时，驱动程序仅发出`CAMERA_TRIGGER` MAVLink消息。需要将命令转发给MAVLink相机，并在任务中发现时自动发送到MAVLink通道。

通过[相机触发参数](../advanced_config/parameter_reference.md#camera-trigger)配置驱动程序。特别是：

- `TRIG_INTERFACE` - 相机如何连接到飞控（PWM、GPIO、Seagull、MAVLink）
- `TRIG_MODE` - 基于距离或时间的触发模式，值由`TRIG_DISTANCE`和`TRIG_INTERVAL`设置。

[设置/使用信息](../camera/index.md)

### 用法 {#camera_trigger_usage}

```
camera_trigger <command> [arguments...]
 命令:
   start

   stop          停止驱动程序

   status        打印驱动程序状态信息

   test          触发一次图像（不记录或转发到地面站）

   test_power    切换电源
```