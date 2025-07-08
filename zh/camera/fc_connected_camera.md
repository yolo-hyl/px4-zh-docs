# 连接到飞行控制器输出的相机

本主题解释如何使用 PX4 与连接到飞行控制器输出的[相机](../camera/index.md)。

::: warning
推荐使用[MAVLink相机](../camera/mavlink_v2_camera.md)，该相机采用[MAVLink Camera Protocol v2](https://mavlink.io/en/services/camera.html)协议。
:::

## 概述

PX4 可通过任务中或地面控制站发送的 [相机命令](#mavlink-command-interface) 触发连接到飞控输出的 [相机](../camera/index.md)。  
支持的命令是 MAVLink [Camera Protocol v1](https://mavlink.io/en/services/camera.html) 定义命令的子集。

每次触发相机时，MAVLink [CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER) 消息会被发布，包含当前会话的图像 _序列号_ 和对应的时间戳。  
该时间戳可用于多种用途，包括：为航拍测绘和重建的照片添加时间戳、同步多相机系统或视觉惯性导航等。

相机可通过不同输出方式连接，包括 PWM 输出、GPIO 输出，以及通过 PWM 输出的 Seagull MAP2。

相机还可通过连接到其热靴的 [相机捕获引脚](#camera-capture-configuration)（可选）在拍摄照片/帧的精确时刻向 PX4 发送信号。  
这允许更精确地将图像映射到 GPS 位置以进行地理标记，或获取正确的 IMU 样本来实现 VIO 同步等。

<!-- Camera trigger driver: https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_trigger -->
<!-- Camera capture driver: https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_capture -->## MAVLink 命令接口

PX4 支持以下 MAVLink 命令，用于与飞控连接的云台相机，在任务中以及从地面站接收时均有效：

- [MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL](#mav-cmd-do-set-cam-trigg-interval) — 设置拍摄时间间隔。
- [MAV_CMD_DO_SET_CAM_TRIGG_DIST](#mav-cmd-do-set-cam-trigg-dist) — 设置拍摄距离间隔。
- [MAV_CMD_DO_TRIGGER_CONTROL](#mav-cmd-do-trigger-control) — 开始/停止拍摄（根据上述消息定义的距离或时间）。
- [MAV_CMD_OBLIQUE_SURVEY](#mav-cmd-oblique-survey) — 开始/停止倾斜测量任务。
- [MAV_CMD_DO_DIGICAM_CONTROL](#mav-cmd-do-digicam-control) — 从地面站测试拍摄相机。

支持的行为如下（这些行为与 MAVLink 规范不完全一致）。

### MAV_CMD_DO_TRIGGER_CONTROL

[MAV_CMD_DO_TRIGGER_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_TRIGGER_CONTROL) - 在 "命令控制" 模式下接受 (`TRIG_MODE` 1)。

| 命令参数 | 描述 |
| -------- | ---- |
| Param #1 | 触发启用/禁用。`1`：启用（开始），`0`：禁用。 |
| Param #2 | 重置触发序列。`1`：重置，其他值无作用。 |
| Param #3 | 暂停触发，但不关闭相机或收起云台。`1`：暂停，`0`：重启。 |

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/camera_trigger/camera_trigger.cpp#L549 -->

### MAV_CMD_DO_DIGICAM_CONTROL

[MAV_CMD_DO_DIGICAM_CONTROL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_DIGICAM_CONTROL) - 在所有模式下接受。

此命令由地面站在用户界面中用于测试拍摄相机。
触发驱动器不支持 MAVLink 规范中定义的所有相机控制参数。

| 命令参数 | 描述 |
| -------- | ---- |
| Param #5 | 触发单次拍摄命令（设为 1 以触发单帧图像）。 |

### MAV_CMD_DO_SET_CAM_TRIGG_DIST

[MAV_CMD_DO_SET_CAM_TRIGG_DIST](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_DIST) - 在 "任务控制" 模式下接受 (`TRIG_MODE` 4)

此命令在任务中自动触发，根据地面站的测绘任务触发相机。

### MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL

[MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_CAM_TRIGG_INTERVAL)

### MAV_CMD_OBLIQUE_SURVEY

[MAV_CMD_OBLIQUE_SURVEY](https://mavlink.io/en/messages/common.html#MAV_CMD_OBLIQUE_SURVEY) - 任务命令，用于设置相机自动云台倾斜测量。

此命令接受 `param1` 至 `param4`，如 MAVLink 消息定义中所述。
快门集成设置 (`param2`) 仅在使用 GPIO 后端时有效。

<!-- https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/camera_trigger/camera_trigger.cpp#L632 -->## 触发配置

相机可通过指定适当的[触发接口后端](#trigger-interface-backends)连接至飞控器进行触发，支持PWM和GPIO等不同接口。您还可以指定相机的[触发模式](#trigger-modes)。

此配置可通过_QGroundControl_ [机体设置 > 相机](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/camera.html#px4-camera-setup)部分最便捷地完成。

![触发引脚](../../assets/camera/trigger_pins.png)

以下描述了不同的[触发模式](#trigger-modes)、[后端接口](#trigger-interface-backends)和[触发输出配置](#trigger-output-pin-configuration)（这些也可通过[参数](../advanced_config/parameters.md)直接设置）。

::: info
基于FMUv2的飞行控制器（例如3DR Pixhawk）默认不包含相机设置部分，因为固件中未自动包含相机模块。更多信息请参见[查找/更新参数 > 固件中未包含的参数](../advanced_config/parameters.md#parameter-not-in-firmware)。
:::

### 触发模式

通过[TRIG_MODE](../advanced_config/parameter_reference.md#TRIG_MODE)参数控制，支持四种不同模式：

| 模式 | 描述                                                                                                                                                                                         |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0    | 禁用相机触发。                                                                                                                                                                      |
| 1    | 通过MAVLink命令`MAV_CMD_DO_TRIGGER_CONTROL`启用/禁用基础定时器功能。详见[命令接口](#mavlink-command-interface)。 |
| 2    | 持续启用定时器功能。                                                                                                                                                          |
| 3    | 基于距离触发。每次设定的水平距离超过时拍摄一张照片，但两次拍摄之间的最短时间间隔由设定的触发间隔限制。      |
| 4    | 在任务模式下执行航测时自动触发。                                                                                                                                        |

::: info
如果您是第一次启用相机触发应用，请在修改`TRIG_MODE`参数后重新启动。
:::

### 触发接口后端

相机触发驱动支持多种后端，通过[TRIG_INTERFACE](../advanced_config/parameter_reference.md#TRIG_INTERFACE)参数控制：

| 编号 | 描述                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1      | 启用GPIO接口。根据`TRIG_POLARITY`参数，AUX输出会在设定的[TRIG_INTERVAL](../advanced_config/parameter_reference.md#TRIG_INTERVAL)间隔内被拉高或拉低。可直接用于触发大多数标准机器视觉相机。注意在PX4FMU系列硬件（Pixhawk、Pixracer等）上，AUX引脚的信号电平为3.3V。                                                                                                                     |
| 2      | 启用Seagull MAP2接口。通过[Seagull MAP2](http://www.seagulluav.com/product/seagull-map2/)可连接多种支持的相机。MAP2的Pin/Channel 1（相机触发）和Pin/Channel 2（模式选择器）应连接至[相机触发引脚配置](#trigger-output-pin-configuration)中映射的低/高引脚。使用Seagull MAP2时，PX4还支持Sony Multiport相机（如QX-1）的自动电源控制和保持功能。 |
| 3      | 启用使用遗留[MAVLink接口](#mavlink-command-interface)的MAVLink相机。当任务中发现相关消息时，消息会自动通过MAVLink `onboard`通道发出。触发相机时，默认通过`onboard`通道发出`CAMERA_TRIGGER` MAVLink消息（若未使用该通道，需要启用自定义流）。[简易MAVLink相机](../camera/mavlink_v1_camera.md)详细说明了此用例。                |
| 4      | 启用通用PWM接口。允许使用[infrared triggers](https://hobbyking.com/en_us/universal-remote-control-infrared-shutter-ir-rc-1g.html)或舵机触发相机。                                                                                                                                                                                                                                                                                                             |

### 触发输出引脚配置

相机触发引脚在_QGroundControl_ [执行器](../config/actuators.md)配置界面设置。

通过将`Camera_Trigger`功能分配到任意FMU输出来设置触发引脚。若使用需要双引脚的触发设置（例如Seagull MAP2），可分配到任意两个输出。

注意，如果使用PWM输出进行相机触发（如Seagull MAP2），整个PWM组将无法用于其他功能（组内其他输出不能用于执行器、电机或相机捕捉，因为定时器已被占用）。

::: info
目前触发功能仅支持FMU引脚：

- 具备FMU和I/O板的Pixhawk飞控器，FMU引脚映射到`AUX`输出（例如Pixhawk 4、CUAV v5+）。
- 仅有FMU的控制器，引脚映射到`MAIN`输出（例如Pixhawk 4 mini、CUAV v5 nano）。
:::

### 其他参数

| 参数                                                                | 描述                                                                                                                                                                                                                      |
| ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [TRIG_POLARITY](../advanced_config/parameter_reference.md#TRIG_POLARITY) | 仅在使用GPIO接口时相关。设置触发引脚的极性。高电平有效表示引脚通常为低电平，在触发事件时拉高；低电平有效则相反。                          |
| [TRIG_INTERVAL](../advanced_config/parameter_reference.md#TRIG_INTERVAL) | 定义两个连续触发事件之间的时间间隔（毫秒）。                                                                                                                                                         |
| [TRIG_ACT_TIME](../advanced_config/parameter_reference.md#TRIG_ACT_TIME) | 定义触发引脚在返回中性状态前保持“激活”状态的时间（毫秒）。在PWM模式下，最小限制为40ms以确保触发脉冲能适配50Hz PWM信号。 |

相机触发模块的完整参数列表请参见[参数参考](../advanced_config/parameter_reference.md#camera-trigger)页面。

## 相机捕捉配置

相机可以（可选）使用相机捕捉引脚来标记拍摄照片/帧的确切时刻。  
这允许将图像更精确地映射到GPS位置用于地理标记，或获取正确的IMU采样用于VIO同步等。

在PX4中通过设置[CAM_CAP_FBACK = 1](../advanced_config/parameter_reference.md#CAM_CAP_FBACK)启用相机捕捉/反馈功能。  
随后在_QGroundControl_的[执行器](../config/actuators.md)配置界面中，通过在任意FMU输出上分配`Camera_Capture`功能来设置相机捕捉使用的引脚。

注意：如果使用_PWM输出_作为相机捕捉输入，则整个PWM组将无法用于其他用途（该组中的其他输出无法用于执行器、电机或相机触发，因为定时器已被占用）。

::: info  
撰写时相机捕捉功能仅支持FMU引脚：  

- 对于同时具备FMU和IO板的Pixhawk飞控（如Pixhawk 4、CUAV v5+），FMU引脚映射到`AUX`输出。  
- 仅具备FMU的控制器（如Pixhawk 4 mini、CUAV v5 nano），引脚映射到`MAIN`输出。  

:::  

PX4会检测相机捕捉引脚上的上升沿及相应电压（Pixhawk飞控通常为3.3V）。  
如果相机未输出合适电压，则需要额外电路使信号兼容。

配备热靴接口（用于连接闪光灯）的相机通常可通过热靴适配器连接。例如：[Seagull #SYNC2 通用相机热靴适配器](https://www.seagulluav.com/product/seagull-sync2/) 是一个光耦，用于解耦并调整闪光灯电压至Pixhawk电压。  
该适配器插入相机顶部的闪光灯插槽，红色和黑色输出连接到舵机电源/地线，白色线连接到输入捕捉引脚。

![Seagull SYNC#2](../../assets/peripherals/camera_capture/seagull_sync2.png)

::: info  
PX4在相机触发和相机捕捉时均会发出MAVLink [CAMERA_TRIGGER](https://mavlink.io/en/messages/common.html#CAMERA_TRIGGER) 消息。  
若配置了相机捕捉，将使用相机捕捉驱动的时戳，否则使用触发时戳。  
:::

## 测试触发功能

:::warning
以下部分已过时，需要重新测试。
:::

1. 在PX4控制台中：

   ```shell
   camera_trigger test
   ```

1. 从 _QGroundControl_:

   在主仪表盘中点击 **Trigger Camera**。
   这些照片不会被记录或用于地理标记。

   ![QGC Test Camera](../../assets/camera/qgc_test_camera.png)

## Sony QX-1 示例（摄影测量）

![photogrammetry](../../assets/camera/photogrammetry.png)

在此示例中，我们将使用 Seagull MAP2 触发线连接到 Sony QX-1 相机，并通过此设置在自主测绘任务飞行后创建正射拼接图。

### 触发设置

推荐的相机参数设置为：

- `TRIG_INTERFACE=2`（Seagull MAP2）。
- `TRIG_MODE=4`（任务控制）。
- 其余参数保持默认值不变。

您需要将 Seagull MAP2 连接到自动驾驶仪的 FMU 引脚。
MAP2 电缆的另一端应插入 QX-1 的 "MULTI" 接口。

### 相机配置

本示例使用配备 16-50mm f3.5-5.6 镜头的 Sony QX-1 相机。

为避免触发相机时的自动对焦和测光延迟，需遵循以下指南：

- 手动对焦至无限远
- 设置为连续拍摄模式
- 手动设置曝光和光圈
- 将 ISO 设置为尽可能低的值
- 根据场景选择合适的手动白平衡

### 任务规划

![QGC Survey Polygon](../../assets/camera/qgc_survey_polygon.jpeg)

![QGC Survey Parameters](../../assets/camera/qgc_survey_parameters.jpg)

### 地理标记

从飞行中下载/复制日志文件和图像，并将 QGroundControl 指向它们。
然后点击 **Start Tagging**。

![QGC Geotagging](../../assets/camera/qgc_geotag.png)

可以使用免费的在线服务如 [Pic2Map](https://www.pic2map.com/) 验证地理标记。
注意 Pic2Map 仅支持最多 40 张图像。

### 三维重建

我们使用 [Pix4D](https://pix4d.com/) 进行三维重建。

![GeoTag](../../assets/camera/geotag.jpg)

## 相机-IMU同步示例（VIO）

在本示例中，我们将介绍如何将IMU测量值与视觉数据同步以构建立体视觉惯性导航系统（VINS）的基本方法。  
需要明确的是，此处的目标不是在拍摄图像时精确同步IMU测量值，而是正确地为图像添加时间戳，以向VIO算法提供准确数据。

自动驾驶仪和伴飞计算机使用不同的时钟基准（自动驾驶仪使用启动时间，伴飞计算机使用UNIX时间戳），因此我们不调整任一时钟，而是直接观察时钟间的时间偏移。  
该偏移量会在跨中间件转换组件（如伴飞计算机上的MAVROS和PX4中的`mavlink_receiver`）中，通过MAVLink消息（例如`HIGHRES_IMU`）的时间戳进行加减操作。  
实际的同步算法是网络时间协议（NTP）的修改版本，使用指数移动平均法平滑跟踪的时间偏移。  
如果使用MAVROS和高带宽机载链路（MAVLink模式`onboard`），此同步过程将自动完成。

为了获取同步的图像帧和惯性测量值，我们将两个相机的触发输入连接到自动驾驶仪上的GPIO引脚。  
从曝光开始到图像序列号的惯性测量时间戳被记录并发送到伴飞计算机（`CAMERA_TRIGGER`消息），伴飞计算机缓冲这些数据包和从相机获取的图像帧。  
它们基于序列号进行匹配（第一帧为序列号0），图像被时间戳（使用`CAMERA_TRIGGER`消息中的时间戳）并发布。

下图说明了正确时间戳图像所需的一系列事件。

[![Mermaid序列图](https://mermaid.ink/img/pako:eNqNUs9rwjAU_lceOW-3nXIQpIoIVkftZIdCeTbPNqxJXJI6ivi_L1Er6Dzs9kK-H3lfviOrjCDGmaPvjnRFE4m1RVVogKXxBFbWjQezg_fPN-CQS0Xgel3Bj_QNKDxY40A6EEYTYOeNQi8rbNs-SkTS62g04DgkqMgi5EG2JguWUPR_vaoLSlh5CKAb63reGuMdoBbR96Zwz7kzvQylcrXjPDFKBe71BYnR3po2ClzhkXnZNR1vFlJ_cR6GMkkn5WRV5tl8NptmZbJa5tlqEXmtMXuYBtMe4m05X-bTbDNegJJtKx1VRgv3NIybQTJOp9l4EH94zGMY99ugmqcfa49q_zyER3aKvmpg-G3QndqS_R_17AJSYU3n9PfdNuzXFJq0YC8sgBVKEbp0jHoF8w0pKhgPo6Addq0vWKFPARp7sg4lYtzbjl5Ytxfoh-oxvsPW0ekXb8TjxQ?type=png)](https://mermaid.live/edit#pako:eNqNUs9rwjAU_lceOW-3nXIQpIoIVkftZIdCeTbPNqxJXJI6ivi_L1Er6Dzs9kK-H3lfviOrjCDGmaPvjnRFE4m1RVVogKXxBFbWjQezg_fPN-CQS0Xgel3Bj_QNKDxY40A6EEYTYOeNQi8rbNs-SkTS62g04DgkqMgi5EG2JguWUPR_vaoLSlh5CKAb63reGuMdoBbR96Zwz7kzvQylcrXjPDFKBe71BYnR3po2ClzhkXnZNR1vFlJ_cR6GMkkn5WRV5tl8NptmZbJa5tlqEXmtMXuYBtMe4m05X-bTbDNegJJtKx1VRgv3NIybQTJOp9l4EH94zGMY99ugmqcfa49q_zyER3aKvmpg-G3QndqS_R_17AJSYU3n9PfdNuzXFJq0YC8sgBVKEbp0jHoF8w0pKhgPo6Addq0vWKFPARp7sg4lYtzbjl5Ytxfoh-oxvsPW0ekXb8TjxQ)

<!-- Original
![Sequence diag](../../assets/camera/sequence_diagram.jpg)
{/% mermaid %/}
sequenceDiagram
  Note right of PX4 : Time sync with mavros is done automatically
  PX4 ->> mavros : Camera Trigger ready
  Note right of camera driver : Camera driver boots and is ready
  camera driver ->> mavros : mavros_msgs::CommandTriggerControl
  mavros ->> PX4 : MAVLink::MAV_CMD_DO_TRIGGER_CONTROL
  loop Every TRIG_INTERVAL milliseconds
  PX4 ->> mavros : MAVLink::CAMERA_TRIGGER
  mavros ->> camera driver : mavros_msgs::CamIMUStamp
  camera driver ->> camera driver : Match sequence number
  camera driver ->> camera driver : Stamp image and publish
end
{/% endmermaid %/}
-->

### 步骤1

首先将TRIG_MODE设置为1，使驱动等待启动命令并重启FCU以获取剩余参数。

### 步骤2

在本示例中，我们将配置触发器与Point Grey Firefly MV相机配合使用，其运行频率为30 FPS。

- `TRIG_INTERVAL`: 33.33 ms
- `TRIG_POLARITY`: 0（低电平有效）
- `TRIG_ACT_TIME`: 0.5 ms。  
  手册规定最低仅需1微秒。
- `TRIG_MODE`: 1，因为我们希望在开始触发前相机驱动已准备好接收图像。  
  这对于正确处理序列号至关重要。

### 步骤3

通过将地线和信号引脚连接到相应位置，将相机连接到AUX端口。

### 步骤4

您需要修改驱动以遵循上述序列图。  
针对[IDS Imaging UEye](https://github.com/ProjectArtemis/ueye_cam)相机和[IEEE1394兼容](https://github.com/andre-nguyen/camera1394)相机的公共参考实现可供使用。

## 另请参见

- 相机触发驱动: [源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_trigger) <!-- no module doc -->
- 相机捕获驱动: [源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/camera_capture) <!-- no module doc -->