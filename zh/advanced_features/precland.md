# 精确着陆

PX4 支持 _多旋翼_ 无人机在静止或移动目标上进行精确着陆。目标信息可以通过机载红外传感器和着陆信标提供，也可以通过外部定位系统提供。

精确着陆可通过以下方式启动：作为 [任务](#mission) 的一部分、在 [返回模式](#返回模式精确着陆) 着陆中，或通过进入 [_精确着陆_ 飞行模式](#精确着陆飞行模式)。

::: info
由于位置控制器当前实现的限制，精确着陆需要有效的全局位置。
:::

## 概述

### 陆地模式

精确着陆可以配置为"必选"或"机会"模式。  
模式选择会影响精确着陆的执行方式。

#### 必选模式

在 _必选模式_ 中，机体将在着陆开始时若未发现目标则会主动搜索目标。  
若定位到目标，机体将执行精确着陆。

搜索流程包括爬升到搜索高度（[PLD_SRCH_ALT](../advanced_config/parameter_reference.md#PLD_SRCH_ALT)）。  
若在搜索高度仍未发现目标且超过搜索超时时间（[PLD_SRCH_TOUT](../advanced_config/parameter_reference.md#PLD_SRCH_TOUT)），则会在当前位置执行普通着陆。

::: info
若使用外部定位系统，PX4会认为接收到MAVLink [LANDING_TARGET](https://mavlink.io/en/messages/common.html#LANDING_TARGET)消息时目标可见。
:::

#### 机会模式

在 _机会模式_ 中，机体仅在着陆开始时若目标可见（且仅当可见时）才执行精确着陆。  
若目标不可见，机体将立即在当前位置执行普通着陆。

### 着陆阶段

精确着陆包含三个阶段：

1. **水平接近：** 机体在保持当前高度的同时水平接近目标。  
   当目标相对于机体的位置误差小于阈值（[PLD_HACC_RAD](../advanced_config/parameter_reference.md#PLD_HACC_RAD)）时进入下一阶段。  
   若此阶段丢失目标（持续不可见时间超过 [PLD_BTOUT](../advanced_config/parameter_reference.md#PLD_BTOUT)），将启动搜索流程（必选精确着陆时）或执行普通着陆（机会精确着陆时）。

1. **目标上方下降：** 机体在目标正上方保持居中位置下降。  
   若此阶段丢失目标（持续不可见时间超过 `PLD_BTOUT`），将启动搜索流程（必选精确着陆时）或执行普通着陆（机会精确着陆时）。

1. **最终接近：** 当机体接近地面（高度低于 [PLD_FAPPR_ALT](../advanced_config/parameter_reference.md#PLD_FAPPR_ALT)）时，保持居中位置下降。  
   若此阶段丢失目标，将继续下降且不受精确着陆类型的限制。

搜索流程在第一和第二阶段启动，最多执行 [PLD_MAX_SRCH](../advanced_config/parameter_reference.md#PLD_MAX_SRCH) 次。  
着陆阶段流程图

着陆阶段的流程图请参见下文的[着陆阶段流程图](#降落阶段流程图)。

## 启动精确着陆

精确着陆可在任务中使用，在_Return mode_模式的着陆阶段使用，或通过进入_Precision Land_模式使用。

<a id="mission"></a>

### 任务精确着陆

通过[mission](../flying/missions.md)使用[MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND)并适当设置`param2`参数可以启动精确着陆：

- `0`: 不使用目标的普通着陆。
- `1`: [机会式](#机会模式)精确着陆。
- `2`: [必要式](#必选模式)精确着陆。

### 返回模式精确着陆

精确着陆可在[Return mode](../flight_modes_mc/return.md)的着陆阶段使用。

通过参数[RTL_PLD_MD](../advanced_config/parameter_reference.md#RTL_PLD_MD)启用，该参数取值如下：

- `0`: 禁用精确着陆（按普通方式着陆）。
- `1`: [机会式](#机会模式)精确着陆。
- `2`: [必要式](#必选模式)精确着陆。

### 精确着陆飞行模式

通过切换到_Precision Landing_飞行模式可启用精确着陆。

可使用[_QGroundControl_ MAVLink Console](../debug/mavlink_shell.md#qgroundcontrol-mavlink-console)输入以下命令验证：

```sh
commander mode auto:precland
```

::: info
通过此方式切换模式时，精确着陆始终为"必要式"；无法指定着陆类型。
:::

::: info
目前没有直接调用精确着陆的便捷方式（除返回模式指令外）：

- _QGroundControl_未提供该功能的UI选项。
- [MAV_CMD_NAV_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_LAND)仅在任务中有效。
- [MAV_CMD_DO_SET_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_MODE)理论上可用，但需要确定PX4中表示精确着陆模式的基模式和自定义模式。
  :::

## 硬件设置

### IR 传感器/信标设置

IR 传感器/着陆信标解决方案需要一个 [IR-LOCK 传感器](https://irlock.com/products/ir-lock-sensor-precision-landing-kit) 和一个向下朝向的 [距离传感器](../sensor/rangefinders.md) 连接到飞控器，并使用 IR 信标作为目标（例如 [IR-LOCK MarkOne](https://irlock.com/collections/markone)）。  
这可实现约 10 厘米精度的着陆（相比之下，GPS 的精度可能达到几米）。

按照 [官方指南](https://irlock.readme.io/v2.0/docs) 安装 IR-LOCK 传感器。  
确保传感器的 x 轴与机体的 y 轴对齐，传感器的 y 轴与机体的 -x 方向对齐（如果摄像头向下俯仰 90 度朝向正前方时即满足此条件）。

安装一个 [范围/距离传感器](../sensor/rangefinders.md)（_LidarLite v3_ 已被证实效果良好）。

::: info
许多基于红外的距离传感器在存在 IR-LOCK 信标时性能不佳。  
请参考 IR-LOCK 指南查看兼容的传感器列表。
:::

## 外部定位

外部定位解决方案需要实现MAVLink [Landing Target Protocol](https://mavlink.io/en/services/landing_target.html)的定位系统。
该系统可以使用任何定位机制来确定着陆目标，例如计算机视觉和视觉标记。

系统必须通过[LANDING_TARGET](https://mavlink.io/en/messages/common.html#LANDING_TARGET)消息发布目标坐标。
注意PX4 _要求_ `LANDING_TARGET.frame` 必须为[MAV_FRAME_LOCAL_NED](https://mavlink.io/en/messages/common.html#MAV_FRAME_LOCAL_NED)，且仅填充`x`、`y`和`z`字段。
本地NED坐标系的原点[0,0]是起始位置（可以使用[GPS_GLOBAL_ORIGIN](https://mavlink.io/en/messages/common.html#GPS_GLOBAL_ORIGIN)将该起始位置映射到全局坐标）。

PX4不明确要求有[距离传感器](../sensor/rangefinders.md)或其他传感器，但如果能更精确地确定自身位置，其表现会更好。

## 固件配置

精确着陆需要模块 `irlock` 和 `landing_target_estimator`。  
这些模块默认包含在 PX4 固件中，适用于大多数飞控。  

在基于 FMUv2 的控制器中默认未包含这些模块。  
在这些控制器以及其他未包含的板卡上，您可以通过在对应飞控的配置文件中将以下键值设置为 'y' 来添加它们（例如在 FMUv5 上的实现方式：[PX4-Autopilot/boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board)）：

```
CONFIG_DRIVERS_IRLOCK=y
CONFIG_MODULES_LANDING_TARGET_ESTIMATOR=y
```

## PX4配置（参数）

IR-Lock传感器默认处于禁用状态。
通过将[SENS_EN_IRLOCK](../advanced_config/parameter_reference.md#SENS_EN_IRLOCK)设置为`1`（true）来启用它。

[LTEST_MODE](../advanced_config/parameter_reference.md#LTEST_MODE)确定目标是假设为静止还是移动状态。
如果`LTEST_MODE`设置为移动（例如安装在多旋翼要着陆的机体上），目标测量值仅用于在精确着陆控制器中生成位置设定值。
如果`LTEST_MODE`设置为静止，目标测量值也会被机体位置估计算法（EKF2或LPE）使用。

其他相关参数列在参数参考的[Landing_target estimator](../advanced_config/parameter_reference.md#landing-target-estimator)和[Precision land](../advanced_config/parameter_reference.md#precision-land)参数下。
其中一些最有用的参数如下所示。

| 参数                                                                                             | 描述                                                                                                         |
| ------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| <a id="SENS_EN_IRLOCK"></a>[SENS_EN_IRLOCK](../advanced_config/parameter_reference.md#SENS_EN_IRLOCK) | IR-LOCK传感器（外部I2C）。禁用：`0`（默认）：启用：`1`。                                                |
| <a id="LTEST_MODE"></a>[LTEST_MODE](../advanced_config/parameter_reference.md#LTEST_MODE)         | 着陆目标为移动(`0`)或静止(`1`)。默认为移动。                                              |
| <a id="PLD_HACC_RAD"></a>[PLD_HACC_RAD](../advanced_config/parameter_reference.md#PLD_HACC_RAD)   | 水平接受半径，机体将在该半径内开始下降。默认为0.2m。                      |
| <a id="PLD_BTOUT"></a>[PLD_BTOUT](../advanced_config/parameter_reference.md#PLD_BTOUT)            | 着陆目标超时时间，之后目标被认为丢失。默认为5秒。                               |
| <a id="PLD_FAPPR_ALT"></a>[PLD_FAPPR_ALT](../advanced_config/parameter_reference.md#PLD_FAPPR_ALT) | 最终进近高度。默认为0.1米。                                                                     |
| <a id="PLD_MAX_SRCH"></a>[PLD_MAX_SRCH](../advanced_config/parameter_reference.md#PLD_MAX_SRCH)   | 必要着陆时的最大搜索尝试次数。                                                           |
| <a id="RTL_PLD_MD"></a>[RTL_PLD_MD](../advanced_config/parameter_reference.md#RTL_PLD_MD)         | RTL精确着陆模式。`0`：禁用，`1`：[Opportunistic](#机会模式)，`2`：[Required](#必选模式)。 |

### IR信标缩放

由于IR-LOCK传感器的镜头畸变，可能需要对测量值进行缩放。

[LTEST_SCALE_X](../advanced_config/parameter_reference.md#LTEST_SCALE_X)和[LTEST_SCALE_Y](../advanced_config/parameter_reference.md#LTEST_SCALE_Y)可用于在使用信标测量值估计其相对于机体的位置和速度之前进行缩放。
请注意，`LTEST_SCALE_X`和`LTEST_SCALE_Y`是在传感器坐标系中考虑的，而非机体坐标系。

要校准这些缩放参数，将`LTEST_MODE`设置为移动，在信标上方飞行多旋翼，同时进行前后和左右移动，记录[logging](../dev_log/logging.md#configuration)中的`landing_target_pose`和`vehicle_local_position`。
然后，将`landing_target_pose.vx_rel`和`landing_target_pose.vy_rel`分别与`vehicle_local_position.vx`和`vehicle_local_position.vy`进行比较（所有测量值均在NED坐标系中）。
如果估计的信标速度持续小于或大于机体速度，请调整缩放参数以补偿。

如果在`LTEST_MODE`设置为静止时进行精确着陆过程中观察到机体出现缓慢的横向振荡，很可能信标测量值缩放过高，应在相关方向降低缩放参数。

## 仿真

使用 IR-LOCK 传感器和信标进行的精准着陆可以在 [Gazebo Classic](../sim_gazebo_classic/index.md) 中进行仿真。

要启动包含 IR-LOCK 信标和搭载测距传感器及 IR-LOCK 摄像头的机体的世界仿真，运行：

```sh
make px4_sitl gazebo-classic_iris_irlock
```

您可以通过以下两种方式更改信标的位置：  
1. 在 Gazebo Classic 图形界面中移动信标  
2. 修改 [Gazebo 世界文件](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/iris_irlock.world#L42) 中的信标位置。

## 运行原理

### 降落目标估计器

`landing_target_estimator` 通过 `irlock` 驱动程序的测量数据以及估计的地形高度，计算信标相对于机体的位置。

`irlock_report` 中的测量数据包含从图像中心到信标的角度的正切值。
换句话说，这些测量值是从图像中心指向信标的向量的 x 和 y 分量，其中 z 分量长度为 "1"。
这意味着用相机到信标的距离对测量值进行缩放，即可得到从相机到信标的向量。
该相对位置随后通过机体的姿态估计，旋转到与北方对齐且水平的机体坐标系中。
相对位置的 x 和 y 分量分别在独立的卡尔曼滤波器中进行滤波，这些滤波器充当简单的低通滤波器，同时生成速度估计并允许异常值拒绝。

`landing_target_estimator` 在每次新的 `irlock_report` 融合到估计中时，会发布估计的相对位置和速度。
如果未检测到信标或信标测量被拒绝，则不会发布任何内容。
降落目标估计通过 `landing_target_pose` uORB 消息发布。

### 增强的机体位置估计

如果通过参数 `LTEST_MODE` 指定目标为静止状态，可以借助目标测量值来改进机体的位置/速度估计。
这是通过将目标速度作为机体负速度的测量值进行融合实现的。

### 降落阶段流程图

此图像以流程图形式展示了[降落阶段](#着陆阶段)。

![精确降落流程图](../../assets/precision_land/precland-flow-diagram.png)