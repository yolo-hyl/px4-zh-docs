

# ARK Flow

ARK Flow 是一个开源的 [DroneCAN](index.md) [光流](../sensor/optical_flow.md)、[距离传感器](../sensor/rangefinders.md) 和 IMU 模块。

![ARK Flow](../../assets/hardware/sensors/optical_flow/ark_flow.jpg)

## 购买途径

从以下渠道订购此模块：

- [ARK Electronics](https://arkelectron.com/product/ark-flow/) (US)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_Flow)
- 传感器
  - PixArt PAW3902 光流传感器
    - 在>9 lux的极低光条件下可跟踪
    - 工作范围广，从80毫米到30米
    - 最高达7.4弧度/秒
  - 板载40mW红外LED，用于改善低光环境下的操作
  - Broadcom AFBR-S50LV85D 飞行时间测距传感器
    - 集成850纳米激光光源
    - 视场角（FoV）为12.4° x 6.2°，32像素
    - 典型测距范围可达30米
    - 可在高达200,000 Lux的环境光下工作
    - 适用于所有表面条件
    - 发射光束为2° x 2°，可照亮1至3个像素
  - 博世BMI088六轴IMU或Invensense ICM-42688-P六轴IMU
- STM32F412CEU6 MCU
- 两个Pixhawk标准CAN连接器（4针JST GH）
- Pixhawk标准调试连接器（6针JST SH）
- 可软件切换的内置CAN终端电阻
- 紧凑尺寸
  - 3cm x 3cm x 1.4cm
- LED指示灯
- 美国制造

## 硬件设置

### 接线

ARK Flow通过Pixhawk标准4针JST GH电缆连接到CAN总线。  
如需更多信息，请参考[CAN接线](../can/index.md#wiring)指南。

### 安装

推荐的安装方向是将板载连接器指向**机体后部**，如以下图片所示。

![ARK Flow align with Pixhawk](../../assets/hardware/sensors/optical_flow/ark_flow_orientation.png)

这对应于参数[SENS_FLOW_ROT](../advanced_config/parameter_reference.md#SENS_FLOW_ROT)的默认值(`0`)。
如果使用不同的安装方向，请适当修改该参数。

传感器可以安装在机架的任何位置，但需要在[PX4配置](#PX4配置)期间指定相对于机体重心的焦点位置。

## 固件设置

ARK Flow 运行 [PX4 DroneCAN 固件](px4_cannode_fw.md)。
因此，它支持通过 CAN 总线进行固件更新和 [动态节点分配](index.md#node-id-allocation)。

ARK Flow 板载有预装的最新固件，但如果您想自行构建并烧录最新固件，请参阅 [PX4 DroneCAN 固件 > 构建固件](px4_cannode_fw.md#building-the-firmware)。

- 固件目标：`ark_can-flow_default`
- 引导程序目标：`ark_can-flow_canbootloader`

## 飞行控制器设置

::: info
如果开机时飞行控制器中没有SD卡，Ark Flow将不会启动。
:::

### 启用 DroneCAN

步骤如下：

- 在 _QGroundControl_ 中将参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 设置为 `2` 以启用动态节点分配（如使用 [DroneCAN ESCs](../dronecan/escs.md) 可设置为 `3`），然后重启（参见 [Finding/Updating Parameters](../advanced_config/parameters.md)）。
- 将 ARK Flow CAN 连接到 Pixhawk CAN 接口。

启用后，模块将在启动时被检测到。
流数据应以100Hz频率到达。
距离传感器数据应以40Hz频率到达。

PX4 中的 DroneCAN 配置详情请参见 [DroneCAN > Enabling DroneCAN](../dronecan/index.md#enabling-dronecan)。

### PX4配置

首先设置参数以[启用DroneCAN](#启用 DroneCAN)（如上所示）。
然后设置EKF光流参数以启用光流测量融合用于速度计算，并在传感器未居中安装在机体上时定义偏移量。

在_QGroundControl_中设置以下参数：

- 通过设置[EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL)启用光流融合。
- 如需禁用GPS辅助，将[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)设置为`0`。
- 启用[UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW)。
- 启用[UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG)。
- 将[EKF2_RNG_A_HMAX](../advanced_config/parameter_reference.md#EKF2_RNG_A_HMAX)设置为`10`。
- 将[EKF2_RNG_QLTY_T](../advanced_config/parameter_reference.md#EKF2_RNG_QLTY_T)设置为`0.2`。
- 将[UAVCAN_RNG_MIN](../advanced_config/parameter_reference.md#UAVCAN_RNG_MIN)设置为`0.08`。
- 将[UAVCAN_RNG_MAX](../advanced_config/parameter_reference.md#UAVCAN_RNG_MAX)设置为`30`。
- 将[SENS_FLOW_MINHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MINHGT)设置为`0.08`。
- 将[SENS_FLOW_MAXHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MAXHGT)设置为`25`。
- 将[SENS_FLOW_MAXR](../advanced_config/parameter_reference.md#SENS_FLOW_MAXR)设置为`7.4`以匹配PAW3902最大角流速。
- 参数[EKF2_OF_POS_X](../advanced_config/parameter_reference.md#EKF2_OF_POS_X)、[EKF2_OF_POS_Y](../advanced_config/parameter_reference.md#EKF2_OF_POS_Y)和[EKF2_OF_POS_Z](../advanced_config/parameter_reference.md#EKF2_OF_POS_Z)可用于设置Ark Flow相对于机体重心的偏移量。

当光流是唯一水平位置/速度来源时，建议降低水平位置误差控制器增益[MPC_XY_P](../advanced_config/parameter_reference.md#MPC_XY_P)（例如设为0.5）以减少振荡。

## Ark Flow 配置

在 ARK Flow 上，您可能需要配置以下参数：

| 参数名称                                                                                       | 描述                   |
| ----------------------------------------------------------------------------------------------- | ----------------------------- |
| <a id="CANNODE_TERM"></a>[CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM) | CAN 内置总线终端。 |

## LED含义

在烧录过程中你会在ARK Flow上看到红蓝双色LED，若运行正常则LED为蓝色常亮。

若看到红色LED常亮则表示存在错误，请检查以下内容：

- 确保飞控已安装SD卡
- 在烧录`ark_can-flow_default`前确保Ark Flow已安装`ark_can-flow_canbootloader`
- 清除SD卡根目录和ufw目录下的二进制文件后重新构建并烧录

## 视频

<lite-youtube videoid="SAbRe1fi7bU" params="list=PLUepQApgwSozmwhOo-dXnN33i2nBEl1c0" title="ARK Flow Indoor Position Hold x64"/>

<!-- ARK Flow with PX4 Optical Flow Position Hold: 20210605 -->

_PX4 使用 ARK Flow 传感器进行速度估算保持位置（在 [Position Mode](../flight_modes_mc/position.md) 中）_

## 另请参阅

- [ARK Flow](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-flow) (ARK Docs)