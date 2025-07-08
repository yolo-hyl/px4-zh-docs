# ARK Flow

ARK Flow 是一个开源的 [DroneCAN](index.md) [光流](../sensor/optical_flow.md)、[距离传感器](../sensor/rangefinders.md)和IMU模块。

![ARK Flow](../../assets/hardware/sensors/optical_flow/ark_flow.jpg)

## 购买渠道

可通过以下渠道订购该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-flow/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_Flow)
- 传感器
  - PixArt PAW3902 光流传感器
    - 支持>9 lux超低光条件下追踪
    - 工作范围80mm至30m
    - 最大7.4 rad/s
  - 板载40mW红外LED，提升低光性能
  - Broadcom AFBR-S50LV85D 飞行时间距离传感器
    - 集成850 nm激光光源
    - 视场角12.4° x 6.2°，32像素
    - 典型距离范围至30m
    - 支持200k Lux环境光
    - 适用于所有表面条件
    - 2° x 2°发射光束，可照射1-3个像素
  - Bosch BMI088 6轴IMU或Invensense ICM-42688-P 6轴IMU
- STM32F412CEU6 MCU
- 两个Pixhawk标准CAN接口（4针JST GH）
- Pixhawk标准调试接口（6针JST SH）
- 软件可切换的CAN终端电阻
- 小型化设计
  - 3cm x 3cm x 1.4cm
- LED指示灯
- 美国制造

## 硬件设置

### 接线

通过Pixhawk标准4针JST GH线缆将ARK Flow连接到CAN总线。  
更多细节请参考[CAN接线指南](../can/index.md#wiring)。

### 安装方向

推荐安装方向为板载接口指向**机体后方**，如下图所示。

![ARK Flow与Pixhawk对齐](../../assets/hardware/sensors/optical_flow/ark_flow_orientation.png)

这对应参数 [SENS_FLOW_ROT](../advanced_config/parameter_reference.md#SENS_FLOW_ROT) 的默认值`0`。  
若使用其他方向，请相应调整参数。

该传感器可安装在机架任意位置，但需在[PX4配置](#px4-configuration)中指定相对于重心的焦点位置。

## 固件设置

ARK Flow运行[PX4 DroneCAN固件](px4_cannode_fw.md)，支持通过CAN总线升级固件和[动态节点分配](index.md#node-id-allocation)。

ARK Flow出厂时已预装最新固件。若需自行构建最新固件，详见[PX4 DroneCAN固件 > 构建固件](px4_cannode_fw.md#building-the-firmware)。

- 固件目标: `ark_can-flow_default`
- 引导程序目标: `ark_can-flow_canbootloader`

## 飞控设置

::: info
若飞控中未插入SD卡，Ark Flow将无法启动。
:::

### 启用DroneCAN

操作步骤：

- 在_QGroundControl_中将参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 设置为`2`以启用动态节点分配（或`3`若使用[DroneCAN电调](../dronecan/escs.md)），然后重启（详见[查找/更新参数](../advanced_config/parameters.md)）。
- 将ARK Flow CAN接口连接至Pixhawk CAN接口。

启用后，模块将在启动时被检测到。  
光流数据将以100Hz频率接收，距离传感器数据为40Hz。

PX4中DroneCAN配置的详细说明请参考[DroneCAN > 启用DroneCAN](../dronecan/index.md#enabling-dronecan)。

### PX4配置

首先按上述步骤[启用DroneCAN](#enable-dronecan)，然后设置EKF光流参数以启用光流测量融合，并定义传感器偏移。

在_QGroundControl_中设置以下参数：

- 通过设置 [EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL) 启用光流融合。
- 若需禁用GPS辅助，将 [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) 设置为`0`。
- 启用 [UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW)。
- 启用 [UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG)。
- 将 [EKF2_RNG_A_HMAX](../advanced_config/parameter_reference.md#EKF2_RNG_A_HMAX) 设置为`10`。
- 将 [EKF2_RNG_QLTY_T](../advanced_config/parameter_reference.md#EKF2_RNG_QLTY_T) 设置为`0.2`。
- 将 [UAVCAN_RNG_MIN](../advanced_config/parameter_reference.md#UAVCAN_RNG_MIN) 设置为`0.08`。
- 将 [UAVCAN_RNG_MAX](../advanced_config/parameter_reference.md#UAVCAN_RNG_MAX) 设置为`30`。
- 将 [SENS_FLOW_MINHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MINHGT) 设置为`0.08`。
- 将 [SENS_FLOW_MAXHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MAXHGT) 设置为`25`。
- 将 [SENS_FLOW_MAXR](../advanced_config/parameter_reference.md#SENS_FLOW_MAXR) 设置为`7.4`以匹配PAW3902最大角速度。
- 若传感器不在重心位置，可通过 [EKF2_OF_POS_X](../advanced_config/parameter_reference.md#EKF2_OF_POS_X)、[EKF2_OF_POS_Y](..)、[EKF2_OF_POS_Z](..) 设置偏移量。

当光流为唯一定位来源时，建议将 [EKF2_OF_QMIN](..) 设置为`0.05`以提高稳定性。

## 参数配置

| 参数 | 建议值 | 说明 |
|------|--------|------|
| `EKF2_OF_CTRL` | `1` | 启用光流融合 |
| `EKF2_OF_QMIN` | `0.05` | 光流质量阈值 |
| `EKF2_OF_POS_X` | `0` | X轴偏移 |
| `EKF2_OF_POS_Y` | `0` | Y轴偏移 |
| `EKF2_OF_POS_Z` | `0` | Z轴偏移 |

## 故障排查

- **无法检测到模块**：检查CAN接口连接和终端电阻设置。
- **数据异常**：确认安装方向与 [SENS_FLOW_ROT](..) 参数匹配。
- **固件升级失败**：确保使用最新版QGroundControl，且SD卡未损坏。

## 参考资料

- [ARK Flow](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-flow) (ARK官方文档)