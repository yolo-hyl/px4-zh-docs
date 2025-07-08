# DroneCAN

[DroneCAN](https://dronecan.github.io/) 是一种开放的软件通信协议，用于机体上的飞控和其他 [CAN](../can/index.md) 设备之间的通信。

:::warning

- DroneCAN 默认未启用，使用它的一些特定传感器和功能也未启用。
  有关设置信息，请参见 [PX4 配置](#px4-configuration)。
- PX4 需要 SD 卡以启用动态节点分配和进行固件更新。
  SD 卡在飞行时不会被使用。

:::

::: info
DroneCAN 之前称为 UAVCAN v0（或简称 UAVCAN）。
名称在 2022 年进行了更改。
:::

## DroneCAN 的优势

通过 DroneCAN 连接外设具有许多优势：

- 已支持多种不同的传感器和执行器。
- CAN 协议专为在较远距离内提供可靠、稳定的连接性而设计。
  它使更大的机体能够安全使用电调（ESC），并实现通信冗余。
- 总线支持双向通信，可实现健康监测、诊断和转速遥测（RPM telemetry）。
- 接线更简单，因为可以通过单个总线连接所有电调和其他 DroneCAN 外设。
- 设置更便捷，通过手动旋转每个电机即可配置电调编号。
- 允许用户通过 PX4 集中配置和更新所有 CAN 连接设备的固件。

## 支持的硬件

大部分符合 DroneCAN/UAVCAN v0 标准的常见外设（传感器、电调和舵机）均被支持。

支持的硬件包括（此列表不完整）：

- [电调/电机控制器](../dronecan/escs.md)
- 空速传感器
  - [Holybro 高精度 DroneCAN 空速传感器 - DLVR](https://holybro.com/collections/sensors/products/high-precision-dronecan-airspeed-sensor-dlvr)
  - [RaccoonLab 空速传感器](https://docs.raccoonlab.co/guide/airspeed)
  - [Thiemar 空速传感器](https://github.com/thiemar/airspeed)
- GNSS 接收器（GPS、GLONASS、BeiDou 等）
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
  - [Holybro DroneCAN H-RTK F9P Helical](https://holybro.com/products/dronecan-h-rtk-f9p-helical)
  - [RaccoonLab GNSS 模块](https://docs.raccoonlab.co/guide/gps_mag_baro/)
  - [Zubax GNSS](https://zubax.com/products/gnss_2)
- 电源监控器
  - [Pomegranate Systems 电源模块](../dronecan/pomegranate_systems_pm.md)
  - [CUAV CAN PMU 电源模块](../dronecan/cuav_can_pmu.md)
  - [RaccoonLab CAN 电源连接器和管理单元](../dronecan/raccoonlab_power.md)
- 磁罗盘
  - [Holybro RM3100 专业级磁罗盘](https://holybro.com/products/dronecan-rm3100-compass)
  - [RaccoonLab RM3100 磁力计](https://docs.raccoonlab.co/guide/gps_mag_baro/mag_rm3100.html)
- 距离传感器
  - [ARK Flow](ark_flow.md)
  - [Ark Flow MR](ark_flow_mr.md)
  - [Avionics Anonymous 激光测高仪 UAVCAN 接口](../dronecan/avanon_laser_interface.md)
  - [RaccoonLab uRangefidner 和测距仪适配器](https://docs.raccoonlab.co/guide/rangefinder)
- 光流传感器
  - [Ark Flow](ark_flow.md)
  - [Ark Flow MR](ark_flow_mr.md)

- 通用 CAN 节点（支持在 CAN 总线上使用 I2C、SPI、UART 传感器）
  - [ARK CANnode](../dronecan/ark_cannode.md)
  - [RaccoonLab 节点](../dronecan/raccoonlab_nodes.md)

## 硬件设置

DroneCAN通过CAN网络运行。  
DroneCAN硬件应按照[CAN > 布线](../can/index.md#wiring)中描述的方式进行连接。

## 节点ID分配

每个DroneCAN设备都必须在机体上配置一个唯一的_node id_。

大多数设备支持_Dynamic Node Allocation (DNA)_，这使PX4能够在系统启动时自动为检测到的每个外设分配节点ID。
具体设备是否支持DNA及其启用方式，请参考厂商文档。
许多设备在节点ID设置为0时会自动切换到DNA模式。
当[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)参数值大于1（设置为2或3）时，PX4将启用内置的分配服务器。

部分设备不支持DNA。
此外，在某些关键任务场景中，您可能更倾向于提前手动配置节点ID，而不是依赖动态分配服务器。
若要完全禁用DNA，请将`UAVCAN_ENABLE`设为`1`，并手动为每个节点ID设置唯一值。
如果DNA仍在运行且某些设备需要手动配置，请将这些设备的节点ID值设置为大于DroneCAN设备总数的值，以避免冲突。

::: info
PX4的节点ID可通过[UAVCAN_NODE_ID](../advanced_config/parameter_reference.md#UAVCAN_NODE_ID)参数配置。
该参数默认值为1。
:::

:::warning
截至当前，PX4不会在CAN2端口运行节点分配服务器。
这意味着如果某个设备仅连接到CAN2端口（未冗余连接到CAN1和CAN2），则需要手动配置其节点ID。
:::

## PX4 配置

DroneCAN 在 PX4 上通过 [设置特定 PX4 参数](../advanced_config/parameters.md) 在 QGroundControl 中进行配置。您需要启用 DroneCAN 本身，以及您使用的任何功能的订阅和发布。

::: info
在某些情况下，您还需要配置连接的 CAN 设备上的参数（这些参数也可以 [通过 QGC 设置](#qgc-cannode-parameter-configuration)）。
:::

### 启用 DroneCAN

要启用 PX4 DroneCAN 驱动程序，请设置 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 参数：

- `0`: DroneCAN 驱动禁用
- `1`: DroneCAN 驱动为传感器启用，[DNA 服务器](#node-id-allocation) 禁用
- `2`: DroneCAN 驱动为传感器启用，DNA 服务器启用
- `3`: DroneCAN 驱动为所有功能启用（传感器、执行器等）

::: info
在 PX4 v1.12 之前，选项 `3` 不可用。请参考 [DroneCAN 驱动程序配置](../dronecan/drivers.md) 获取更多详细信息。
:::

### 传感器和外围设备配置

#### GPS

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_GPS](../advanced_config/parameters.md#UAVCAN_SUB_GPS) ([GNSS 位置](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#gnssposition))：用于接收 GPS 数据。
- 启用 [UAVCAN_SUB_GST](../advanced_config/parameters.md#UAVCAN_SUB_GST) ([GNSS PVT](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#gnsspvt))：用于接收 GPS 精度信息。

#### 气压计

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_BARO](../advanced_config/parameters.md#UAVCAN_SUB_BARO) ([气压计](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#barometer))：用于接收气压计数据。

#### 磁力计

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_MAG](../advanced_config/parameters.md#UAVCAN_SUB_MAG) ([磁力计](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#magnetometer))：用于接收磁力计数据。

#### 距离传感器/测距仪

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_RNG](../advanced_config/parameters.md#UAVCAN_SUB_RNG) ([测距](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#ranger))：用于接收测距数据。
- 设置 [UAVCAN_RNG_MIN](../advanced_config/parameters.md#UAVCAN_RNG_MIN) 和 [UAVCAN_RNG_MAX](../advanced_config/parameters.md#UAVCAN_RNG_MAX)：设置测距仪的最小和最大范围。

其他 PX4 参数：

- 如果测距仪不在机体重心位置，可通过 [EKF2_RNG_POS_X](../advanced_config/parameters.md#EKF2_RNG_POS_X) 等参数补偿偏移。
- 其他 `EKF2_RNG_*` 参数可能相关，具体取决于测距仪型号。

#### 光流传感器

PX4 DroneCAN 参数：

- 启用 [UAVCAN_SUB_FLOW](../advanced_config/parameters.md#UAVCAN_SUB_FLOW) ([光流](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#opticalflow))：用于接收光流数据。

其他 PX4 参数：

- 设置 [SENS_FLOW_MINHGT](../advanced_config/parameters.md#SENS_FLOW_MINHGT) 和 [SENS_FLOW_MAXHGT](../advanced_config/parameters.md#SENS_FLOW_MAXHGT)：设置光流传感器的最小和最大高度。
- 设置 [SENS_FLOW_MAXR](../advanced_config/parameters.md#SENS_FLOW_MAXR)：设置传感器的最大角速度。
- 启用光流融合：设置 [EKF2_OF_CTRL](../advanced_config/parameters.md#EKF2_OF_CTRL)。
- 可选禁用 GPS 辅助：设置 [EKF2_GPS_CTRL](../advanced_config/parameters.md#EKF2_GPS_CTRL) 为 `0`。
- 如果光流传感器不在机体重心位置，可通过 [EKF2_OF_POS_X](../advanced_config/parameters.md#EKF2_OF_POS_X) 等参数补偿偏移。

光流传感器需要测距数据。如果测距仪未集成在光流模块中，需要单独配置（见 [测距仪配置](#distance-sensor-range-finder)）。

#### 上锁外围设备

PX4 DroneCAN 参数：

- [UAVCAN_PUB_ARM](../advanced_config/parameters.md#UAVCAN_PUB_ARM) ([上锁状态](https://dronecan.github.io/Specification/7._List_of_standard_data_types/#armingstatus))：当使用需要 PX4 上锁状态作为前提条件的 DroneCAN 组件时发布。

### 电调与舵机

[DroneCAN 电调和舵机](../dronecan/escs.md) 需要配置 [电机顺序和舵机输出](../config/actuators.md)。

## QGC CANNODE参数配置

QGroundControl可以检测并修改连接到飞控的CAN设备参数，前提是设备在启动QGC前已连接到飞控。

CAN节点会以[机体设置 > 参数](../advanced_config/parameters.md)中独立章节的形式显示，标题为_Component X_（X为节点ID）。
例如，下图展示了节点ID为125的CAN GPS参数（在_Standard_、_System_和_Developer_参数分组之后）。

![QGC参数中选中的DroneCAN节点](../../assets/can/dronecan/qgc_can_parameters.png)

## 设备特定设置

大多数 DroneCAN 节点无需进一步设置，除非其设备特定文档中有特别说明。

## 固件更新

PX4 可通过 DroneCAN 升级设备固件。
要升级设备，您只需将固件二进制文件复制到飞控SD卡的根目录，然后重新启动。

飞控在启动时会自动将固件传输到设备并进行升级。
如果升级成功，固件二进制文件将从根目录中删除，并在SD卡的 **/ufw** 目录下生成一个名为 **XX.bin** 的文件。

## 故障排查

**Q**: 我的DroneCAN设备无法工作。

**A**: 请确认`UAVCAN_ENABLE`参数已正确设置。要查看PX4在CAN总线上检测到的设备/节点列表，打开NSH（例如通过QGroundControl的MAVLink控制台）并输入`uavcan status`。

---

**Q**: DNA服务器没有分配节点ID。

**A**: PX4需要SD卡才能执行动态节点分配。请确保已插入（正常工作的）SD卡并重新启动。

---

**Q**: 武装状态下电机未旋转。

**A**: 请确认`UAVCAN_ENABLE`设置为`3`以启用DroneCAN电调输出。

---

**Q**: 直到推油门增加时电机才旋转。

**A**: 使用[Acutator > Actuator Testing](../config/actuators.md#actuator-testing)确认电机输出已设置为正确的最小值。

## 相关链接

- [主页](https://dronecan.github.io) (dronecan.github.io)
- [协议规范](https://dronecan.github.io/Specification) (dronecan.github.io)
- [实现方案](https://dronecan.github.io/Implementations/) (dronecan.github.io)
- [Cyphal/CAN 设备互联](https://kb.zubax.com/pages/viewpage.action?pageId=2195476) (kb.zubax.com)