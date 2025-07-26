

# DroneCAN

[DroneCAN](https://dronecan.github.io/) 是一种开放的软件通信协议，用于机体上的飞控器和其他 [CAN](../can/index.md) 设备之间的通信。

:::warning

- DroneCAN 默认未启用，使用它的特定传感器和功能也是如此。  
  有关设置信息请参见 [PX4 Configuration](#PX4 配置)。
- PX4 需要 SD 卡来启用动态节点分配和进行固件更新。  
  SD 卡在飞行中不会使用。

:::

::: info
DroneCAN 曾被称为 UAVCAN v0（或简称为 UAVCAN）。  
名称于 2022 年更改。
:::

## DroneCAN的优势

通过DroneCAN连接外设具有许多优势：

- 已支持多种不同的传感器和执行器。
- CAN总线专为在较远距离内提供稳健且可靠的连接而设计。它使得在大型机体上安全使用电调以及实现通信冗余成为可能。
- 总线是双向的，可支持健康监测、诊断和RPM遥测。
- 布线更简单，因为可以使用单一总线连接所有电调和其他DroneCAN外设。
- 设置更简单，因为您可以通过手动旋转每个电机来配置电调编号。
- 它允许用户通过PX4集中配置和更新所有通过CAN连接的设备的固件。

## 支持的硬件

大多数符合 DroneCAN/UAVCAN v0 标准的常见外设（传感器、电调和舵机）均受支持。

支持的硬件包括（以下非完整列表）：

- [电调/电机控制器](../dronecan/escs.md)
- 空速传感器
  - [Holybro 高精度 DroneCAN 空速传感器 - DLVR](https://holybro.com/collections/sensors/products/high-precision-dronecan-airspeed-sensor-dlvr)
  - [RaccoonLab 空速传感器](https://docs.raccoonlab.co/guide/airspeed)
  - [Thiemar 空速传感器](https://github.com/thiemar/airspeed)
- GNSS 接收器（GPS、GLONASS、北斗等）
  - [ARK GPS](../dronecan/ark_gps.md)
  - [ARK RTK GPS](../dronecan/ark_rtk_gps.md)
  - [CubePilot Here3](https://www.cubepilot.org/#/here/here3)
  - [CUAV NEO v2 Pro GNSS](https://doc.cuav.net/gps/neo-series-gnss/en/neo-v2-pro.html)
  - [CUAV NEO 3 Pro GPS](../gps_compass/gps_cuav_neo_3pro.md)
  - [CUAV NEO 3X GPS](../gps_compass/gps_cuav_neo_3x.md)
  - [CUAV C-RTK2 PPK/RTK GNSS](../gps_compass/rtk_gps_cuav_c-rtk2.md)
  - [Holybro H-RTK ZED-F9P (DroneCAN 版本)](../dronecan/holybro_h_rtk_zed_f9p_gps.md)
  - [Holybro DroneCAN M8N GPS](../dronecan/holybro_m8n_gps.md)
  - [Holybro DroneCAN M9N GPS](https://holybro.com/products/dronecan-m9n-gps)
  - [Holybro DroneCAN H-RTK F9P Rover](https://holybro.com/products/dronecan-h-rtk-f9p-rover)
  - [Holybro DroneCAN H-RTK F9P 螺旋桨](https://holybro.com/products/dronecan-h-rtk-f9p-helical)
  - [RaccoonLab GNSS 模块](https://docs.raccoonlab.co/guide/gps_mag_baro/)
  - [Zubax GNSS](https://zubax.com/products/gnss_2)
- 电源监测器
  - [Pomegranate Systems 电源模块](../dronecan/pomegranate_systems_pm.md)
  - [CUAV CAN PMU 电源模块](../dronecan/cuav_can_pmu.md)
  - [RaccoonLab CAN 电源连接器和管理单元](../dronecan/raccoonlab_power.md)
- 磁力计
  - [Holybro RM3100 专业级磁力计](https://holybro.com/products/dronecan-rm3100-compass)
  - [RaccoonLab RM3100 磁强计](https://docs.raccoonlab.co/guide/gps_mag_baro/mag_rm3100.html)
- 距离传感器
  - [ARK Flow](ark_flow.md)
  - [Ark Flow MR](ark_flow_mr.md)
  - [Avionics Anonymous 激光高度计 UAVCAN 接口](../dronecan/avanon_laser_interface.md)
  - [RaccoonLab uRangefidner 和测距仪适配器](https://docs.raccoonlab.co/guide/rangefinder)
- 光流传感器
  - [Ark Flow](ark_flow.md)
  - [Ark Flow MR](ark_flow_mr.md)

- 通用 CAN 节点（支持在 CAN 总线上使用 I2C、SPI、UART 传感器）
  - [ARK CANnode](../dronecan/ark_cannode.md)
  - [RaccoonLab 节点](../dronecan/raccoonlab_nodes.md)

## 硬件设置

DroneCAN 通过 CAN 网络运行。  
DroneCAN 硬件应按照 [CAN > 接线](../can/index.md#wiring) 中的描述连接。

## 节点ID分配

每台DroneCAN设备必须配置一个在机体上唯一的_node id_。

大多数设备支持_Dynamic Node Allocation (DNA)_，这允许PX4在系统启动时自动为每个检测到的外设配置节点ID。
请查阅制造商文档以获取详细信息，了解您的设备是否支持DNA以及如何启用该功能。
许多设备在节点ID设置为0时会自动切换到DNA模式。
当[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)参数值大于1（设置为2或3）时，PX4将启用内置的分配服务器。

某些设备不支持DNA。
此外，在关键任务场景中，您可能更倾向于提前手动配置节点ID，而不是依赖动态分配服务器。
如果希望完全禁用DNA，请将`UAVCAN_ENABLE`设为`1`，并手动为每个节点ID设置唯一值。
如果DNA仍在运行且某些设备需要手动配置，请将这些设备的ID值设置为大于DroneCAN设备总数，以避免冲突。

::: info
PX4的节点ID可通过[UAVCAN_NODE_ID](../advanced_config/parameter_reference.md#UAVCAN_NODE_ID)参数进行配置。
该参数默认值为1。
:::

:::warning
截至本文撰写时，PX4不会在CAN2端口运行节点分配服务器。
这意味着如果您有一个设备仅连接到CAN2（而非同时冗余连接到CAN1和CAN2），则需要手动配置其节点ID。
:::

## PX4 配置

通过在 QGroundControl 中[设置特定的 PX4 参数](../advanced_config/parameters.md)来配置 DroneCAN。您需要启用 DroneCAN 本身，以及为所使用的任何功能启用订阅和发布。

::: info
在某些情况下，您还需要在连接的 CAN 设备上配置参数（这些参数也可以通过 [QGC](#QGC CAN节点参数配置) 进行设置）。
:::

### 启用DroneCAN

要启用PX4的DroneCAN驱动程序，请设置参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)：

- `0`: DroneCAN驱动禁用
- `1`: 传感器启用DroneCAN驱动，[DNA server](#节点ID分配)禁用
- `2`: 传感器启用DroneCAN驱动，DNA server启用
- `3`: 传感器和电调启用DroneCAN驱动，DNA server启用

如果DNA被支持，建议使用`2`或`3`。

### DroneCAN 订阅与发布  

PX4 默认不会发布或订阅可能需要的 DroneCAN 消息，以避免占用 CAN 总线。  
相反，必须通过设置关联的 [UAVCAN 参数](../advanced_config/parameter_reference.md#uavcan) 来启用特定功能的发布或订阅。  

::: info  
在启用关联的 DroneCAN [传感器订阅](#传感器) 之前，传感器参数可能无法（在 QGC 中）显示！  

例如，[SENS_FLOW_MINHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MINHGT) 参数只有在启用 [UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW) 后才会存在。  
:::  

例如，要使用连接的 DroneCAN 智能电池，需启用 [UAVCAN_SUB_BAT](../advanced_config/parameter_reference.md#UAVCAN_SUB_BAT) 参数，这将使 PX4 订阅接收 [BatteryInfo](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#batteryinfo) DroneCAN 消息。  
如果外设需要知道 PX4 是否处于解锁状态，需设置 [UAVCAN_PUB_ARM](../advanced_config/parameter_reference.md#UAVCAN_PUB_ARM) 参数，使 PX4 开始发布 [ArmingStatus](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#armingstatus) 消息。  

参数名以 `UAVCAN_SUB_` 和 `UAVCAN_PUB_` 为前缀，用于指示 PX4 是订阅还是发布消息。  
名称的其余部分表示正在设置的具体消息/功能。  

连接到 PX4 的 DroneCAN 外设也可以通过 [QGC 使用参数进行配置](#QGC CAN节点参数配置)。  
按惯例，以 [CANNODE\_](../advanced_config/parameter_reference.md#CANNODE_BITRATE) 为前缀的参数具有预定义含义，可能在参数参考中进行说明。  
以 `CANNODE_PUB_` 和 `CANNODE_SUB_` 为前缀的 `CANNODE_` 参数允许外设发布或订阅关联的 DroneCAN 消息。  
这些功能允许 DroneCAN 外设仅订阅和发布它们实际需要的消息（与 PX4 使用对应的 `UAVCAN_PUB_`/`UAVCAN_SUB_` 参数方式相同）。  
请注意，某些外设可能不会使用 `CANNODE_` 参数，此时它们可能需要根据是否需要发布/订阅特定消息。  

以下部分将详细说明用于启用特定功能的 PX4 和 DroneCAN 外设参数。

#### 传感器

你可以启用的DroneCAN传感器参数/订阅项如下（在PX4 v1.14中）：

- [UAVCAN_SUB_ASPD](../advanced_config/parameter_reference.md#UAVCAN_SUB_ASPD): 空速
- [UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO): 气压计
- [UAVCAN_SUB_BAT](../advanced_config/parameter_reference.md#UAVCAN_SUB_BAT): 电池监控/电源模块
- [UAVCAN_SUB_BTN](../advanced_config/parameter_reference.md#UAVCAN_SUB_BTN): 按钮
- [UAVCAN_SUB_DPRES](../advanced_config/parameter_reference.md#UAVCAN_SUB_DPRES): 压差
- [UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW): 光流
- [UAVCAN_SUB_GPS](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS): GPS
- [UAVCAN_SUB_GPS_R](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS_R)<Badge type="tip" text="PX4 v1.15" />: 订阅GNSS相对消息([RelPosHeading](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#relposheading))。
仅在PX4 v1.15中用于日志记录。
- [UAVCAN_SUB_HYGRO](../advanced_config/parameter_reference.md#UAVCAN_SUB_HYGRO): 湿度计
- [UAVCAN_SUB_ICE](../advanced_config/parameter_reference.md#UAVCAN_SUB_ICE): 内燃机(ICE)。
- [UAVCAN_SUB_IMU](../advanced_config/parameter_reference.md#UAVCAN_SUB_IMU): IMU
- [UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG): 磁力计(指南针)
- [UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG): 测距仪(距离传感器)

#### GPS

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_GPS](../advanced_config/parameter_reference.md#UAVCAN_SUB_GPS)。
- 如果 GPS 模块内置罗盘，请启用 [UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG)。

GPS CANNODE 参数 ([通过 QGC 设置](#QGC CAN节点参数配置))：

- 将 [CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM) 设置为 `1` 以设置 CAN 总线上的最后一个节点。

其他 PX4 参数：

- 如果 GPS 未位于机体质心，可使用 [EKF2_GPS_POS_X](../advanced_config/parameter_reference.md#EKF2_GPS_POS_X)、[EKF2_GPS_POS_Y](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Y) 和 [EKF2_GPS_POS_Z](../advanced_config/parameter_reference.md#EKF2_GPS_POS_Z) 进行偏移补偿。
- 如果 GPS 模块提供偏航信息，可通过将 [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) 的第 3 位设置为 true 来启用 GPS 偏航融合。

#### RTK GPS

设置与上述[GPS](#GPS)相同的参数。
此外，根据您的RTK setup是Rover和Fixed Base，还是Rover和Moving Base，或者是两者的组合，您可能还需要设置以下参数。

##### Rover和Fixed Base

Rover的位置通过RTK基准模块发送的RTCM消息建立（基准模块连接到QGC，QGC通过MAVLink将RTCM信息发送给PX4）。

PX4 DroneCAN参数：

- [UAVCAN_PUB_RTCM](../advanced_config/parameter_reference.md#UAVCAN_PUB_RTCM)：
  - 使PX4向总线发布RTCM消息（[RTCMStream](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#rtcmstream)），这些消息通过QGC从RTK基准模块获取。

Rover模块参数（也可通过QGC设置）：

- [CANNODE_SUB_RTCM](../advanced_config/parameter_reference.md#CANNODE_SUB_RTCM) 告诉Rover订阅总线上的[RTCMStream](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#rtcmstream) RTCM消息（来自移动基准站）。

::: info
也可以使用[UAVCAN_PUB_MBD](../advanced_config/parameter_reference.md#UAVCAN_PUB_MBD)和[CANNODE_SUB_MBD](../advanced_config/parameter_reference.md#CANNODE_SUB_MBD)，它们同样会发布RTCM消息（这些是更新的参数）。
使用[RTCMStream](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#rtcmstream)消息意味着可以同时实现移动基准（见下文）。
:::

##### 漫游器和移动基准站

如在[RTK GPS航向使用双u-blox F9P](../gps_compass/u-blox_f9p_heading.md)中讨论的，机体可以配备两个RTK模块以通过GPS计算偏航角。
在此配置中，机体拥有的是移动基准站RTK GPS和漫游器RTK GPS模块。

这些参数可以分别在[移动基准站和漫游器RTK CAN节点](#QGC CAN节点参数配置)上设置：

- [CANNODE_PUB_MBD](../advanced_config/parameter_reference.md#CANNODE_PUB_MBD) 使移动基准站GPS单元在总线上发布[MovingBaselineData](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#movingbaselinedata) RTCM消息（供漫游器使用）
- [CANNODE_SUB_MBD](../advanced_config/parameter_reference.md#CANNODE_SUB_MBD) 告知漫游器它应在总线上订阅[MovingBaselineData](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#movingbaselinedata) RTCM消息（来自移动基准站）。

对于PX4，还需要设置[GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)以表示移动基准站和漫游器的相对位置：如果您的漫游器位于移动基准站前方，值为0；如果漫游器位于移动基准站右侧，值为90；如果漫游器位于移动基准站后方，值为180；如果漫游器位于移动基准站左侧，值为270。

#### 气压计

PX4 DroneCAN 参数:

- 启用 [UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO).

#### 指南针

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_MAG](../advanced_config/parameter_reference.md#UAVCAN_SUB_MAG).

#### 距离传感器/测距仪

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG)。
- 设置 [UAVCAN_RNG_MIN](../advanced_config/parameter_reference.md#UAVCAN_RNG_MIN) 和 [UAVCAN_RNG_MAX](../advanced_config/parameter_reference.md#UAVCAN_RNG_MAX)，分别表示距离传感器的最小和最大量程。

其他 PX4 参数：

- 如果测距仪未安装在机体质心位置，可通过 [EKF2_RNG_POS_X](../advanced_config/parameter_reference.md#EKF2_RNG_POS_X)、[EKF2_RNG_POS_Y](../advanced_config/parameter_reference.md#EKF2_RNG_POS_Y) 和 [EKF2_RNG_POS_Z](../advanced_config/parameter_reference.md#EKF2_RNG_POS_Z) 设置偏移量。
- 其他 `EKF2_RNG_*` 参数可能相关，具体应参考特定测距仪的文档说明。

#### 光学流传感器

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW)。

其他 PX4 参数：

- 设置 [SENS_FLOW_MINHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MINHGT) 和 [SENS_FLOW_MAXHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MAXHGT)，即光学流传感器的最小和最大高度。
- 设置 [SENS_FLOW_MAXR](../advanced_config/parameter_reference.md#SENS_FLOW_MAXR)，即传感器的最大角流速。
- 通过设置 [EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL) 启用光学流融合。
- 要禁用 GPS 辅助（可选），将 [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) 设为 `0`。
- 如果光学流传感器未安装在机体重心，可通过 [EKF2_OF_POS_X](../advanced_config/parameter_reference.md#EKF2_OF_POS_X)、[EKF2_OF_POS_Y](../advanced_config/parameter_reference.md#EKF2_OF_POS_Y) 和 [EKF2_OF_POS_Z](../advanced_config/parameter_reference.md#EKF2_OF_POS_Z) 调整偏移量。

光学流传感器需要测距仪数据。
然而测距仪不必属于同一模块，且如果未集成，可能不会通过 DroneCAN 连接。
如果测距仪通过 DroneCAN 连接（无论是否内置或独立），还需要按照 [测距仪部分](#distance-sensor-range-finder)（上方）的说明启用。

#### 启用外设

PX4 DroneCAN 参数：

- [UAVCAN_PUB_ARM](../advanced_config/parameter_reference.md#UAVCAN_PUB_ARM) ([启用状态](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#armingstatus))：当使用需要 PX4 启用状态作为使用前提条件的 DroneCAN 组件时发布。

### 电调 & 舵机

[DroneCAN电调和舵机](../dronecan/escs.md) 需要配置 [电机顺序和舵机输出](../config/actuators.md)。

## QGC CAN节点参数配置

QGroundControl可以检查和修改连接到飞控的CAN设备参数，前提是这些设备在QGC启动前已连接到飞控。

CAN节点以独立部分显示在[机体设置 > 参数](../advanced_config/parameters.md)中，命名为_Component X_，其中X是节点ID。  
例如，下图显示了一个节点ID为125的CAN GPS的参数（在_Standard_、_System_和_Developer_参数分组之后）。

![QGC参数显示所选DroneCAN节点](../../assets/can/dronecan/qgc_can_parameters.png)

## 设备特定设置

大多数DroneCAN节点不需要进一步设置，除非在其设备特定文档中有特别说明。

## 固件更新

PX4可以通过DroneCAN升级设备固件。
要升级设备，只需将固件二进制文件复制到飞控SD卡的根目录，然后重启即可。

飞控启动时会自动将固件传输到设备并完成升级。
如果升级成功，固件二进制文件将从根目录删除，并在SD卡的**/ufw**目录中生成一个名为**XX.bin**的文件。

## 故障排除

**Q**: 我的 DroneCAN 设备无法工作。

**A**: 检查 `UAVCAN_ENABLE` 参数是否设置正确。要查看 PX4 在 CAN 总线上检测到的设备/节点列表，请打开 NSH（例如前往 QGroundControl 的 MAVLink 控制台），然后输入 `uavcan status`。

---

**Q**: DNA 服务器没有分配节点 ID。

**A**: PX4 要求使用 SD 卡进行动态节点分配。请确保已插入（可用的）一张 SD 卡并重启。

---

**Q**: 上锁后电机没有转动。

**A**: 请确保将 `UAVCAN_ENABLE` 设置为 `3` 以启用 DroneCAN 电调输出。

---

**Q**: 直到推油门电机才开始转动。

**A**: 使用 [执行器 > 执行器测试](../config/actuators.md#actuator-testing) 确认电机输出已设置为正确的最小值。

## 有用链接

- [主页](https://dronecan.github.io) (dronecan.github.io)
- [协议规范](https://dronecan.github.io/Specification) (dronecan.github.io)
- [实现](https://dronecan.github.io/Implementations/) (drone.com)
- [Cyphal/CAN设备互联](https://kb.zubax.com/pages/viewpage.action?pageId=2195476) (kb.zubax.com)