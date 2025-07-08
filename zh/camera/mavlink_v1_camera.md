# 简单的MAVLink相机（相机协议v1）

本主题解释如何使用PX4与实现[相机协议v1（简单触发协议）](https://mavlink.io/en/services/camera_v1.html)的[MAVLink相机](../camera/index.md)，以及如何与地面站配合工作。

::: warning
使用[MAVLink相机](../camera/mavlink_v2_camera.md)和[MAVLink相机协议v2](https://mavlink.io/en/services/camera.html)的版本在可能时应优先使用！
此方法保留用于兼容较旧的MAVLink相机。
:::

## 概述

[相机协议v1](https://mavlink.io/en/services/camera_v1.html)定义了一组命令，可用于以下相机触发：

- 基于时间或距离的静态图像捕捉频率
- 视频捕捉
- 有限的相机配置

PX4支持通过本协议的原生协议触发相机（如本主题所述），也支持[连接到飞控输出的相机](../camera/fc_connected_camera.md)。

地面站和MAVLink SDK通常将相机命令发送给飞控，飞控随后将其转发到`onboard`类型的MAVLink通道。
PX4还会将任务中遇到的任何相机任务项重新发出为相机命令：未被接受的命令将被记录。
在所有情况下，命令均以飞控的系统ID和组件ID 0（即发送给所有组件，包括相机）发出。

每当触发图像捕捉时，PX4也会发出一个[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER)（相机本身在触发时也可能发出此消息）。

## 控制相机

### MAVLink命令与消息

[相机协议v1（简单触发协议）](https://mavlink.io/en/services/camera_v1.html)定义了以下命令：

- [MAV_CMD_DO_TRIGGER_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_TRIGGER_CONTROL)
- [MAV_CMD_NAV_CMD_DO_DIGICAM_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_CMD_DO_DIGICAM_CONTROL)
- [MAV_CMD_DO_SET_CAM_TRIGG_DIST](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_DIST)
- [MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL)
- [MAV_CMD_OBLIQUE_SURVEY](https://mavlink.io/en/messages/common.html#MAV_CMD_OBLIQUE_SURVEY)
- [MAV_CMD_DO_CONTROL_VIDEO](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_CONTROL_VIDEO)

MAVLink相机将支持这些命令的子集。
由于协议没有功能发现过程，唯一的判断方式是通过检查[COMMAND_ACK](https://mavlink.io/en/messages/common.html#COMMAND_ACK)返回的结果。

相机应在每次捕捉图像时发出[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER)。

[相机协议v1](https://mavlink.io/en/services/camera_v1.html)更详细地描述了该协议。

### 地面站

地面站可以使用[相机协议v1（简单触发协议）](https://mavlink.io/en/services/camera_v1.html)中的任何命令，并应发送到飞控组件ID。
如果相机不支持这些命令，它将返回带有错误结果的[COMMAND_ACK](https://mavlink.io/en/messages/common.html#COMMAND_ACK)。

通常命令发送到飞控，因为无论相机是通过MAVLink连接还是直接连接到飞控，这种方式都适用。
如果发送到飞控，PX4将在每次捕捉图像时发出[CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER)，并可能记录相机捕捉事件。

<!-- "May" because the camera feedback module is "supposed"  to log just camera capture from a capture pin connected to camera hotshoe, but currently logs all camera trigger events from the camera trigger driver https://github.com/PX4/PX4-Autopilot/pull/23103 -->

理论上，您也可以直接向相机发送命令。

### 任务中的相机命令

以下[相机协议v1（简单触发协议）](https://mavlink.io/en/services/camera_v1.html)命令可用于任务（与上述列表相同）：

- [MAV_CMD_DO_TRIGGER_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_TRIGGER_CONTROL)
- [MAV_CMD_NAV_CMD_DO_DIGICAM_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_CMD_DO_DIGICAM_CONTROL)
- [MAV_CMD_DO_SET_CAM_TRIGG_DIST](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_DIST)
- [MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL](https
://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL)
- [MAV_CMD_OBLIQUE_SURVEY](https://mavlink.io/en/messages/common.html#MAV_CMD_OBLIQUE_SURVEY)
- [MAV_CMD_DO_CONTROL_VIDEO](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_CONTROL_VIDEO)

PX4会以飞控相同的系统ID和[MAV_COMP_ID_ALL](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_ALL)组件ID重新发出这些命令：

<!-- See camera_architecture.md topic for detail on how this is implemented -->

### 手动控制

不支持使用这些相机进行手动触发（无论是游戏手柄还是遥控器）。

## 配置

### MAVLink端口和转发配置

要修改一个未使用的`MAV_n_CONFIG`参数，例如`MAV_2_CONFIG`，以配置MAVLink通道：

1. 修改一个未使用的`MAV_n_CONFIG`参数，例如`MAV_2_CONFIG`，设置为`192.168.1.1:14550`。
2. 保存配置并重启飞控。

### 相机模式和触发设置

使用QGroundControl进行配置：

1. 打开[机体设置 > 相机]。
2. 设置[TRIG_MODE](...)为`DISTANCE`。
3. 设置[TRIG_DIST](...)为`10`米。
4. 保存配置并重启飞控。

具体参数配置步骤请参考QGroundControl的用户手册。