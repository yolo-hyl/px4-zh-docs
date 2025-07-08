# ARK Flow MR

ARK Flow MR（"Mid Range"）是一个开源的 [DroneCAN](index.md) [光流](../sensor/optical_flow.md)、[距离传感器](../sensor/rangefinders.md)和IMU模块。  
它是新一代的 [Ark Flow](ark_flow.md)，专为中距离应用场景设计。

![ARK Flow MR](../../assets/hardware/sensors/optical_flow/ark_flow_mr.jpg)

## 购买渠道

可通过以下渠道购买该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-flow-mr/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_Flow_MR)
- 传感器
  - PixArt PAA3905 光流传感器
    - 自动检测复杂条件，如棋盘格、条纹、光泽表面和偏航
    - 工作范围从80mm到无限远
    - 自动切换操作模式
    - 板载40mW红外LED以改善低光环境性能
  - Broadcom AFBR-S50LX85D 飞行时间距离传感器
    - 激光开口角度2° x 2°
    - 典型距离范围达50m
    - 可在高达200千勒克斯的环境光下运行
    - 适用于所有表面条件
- Invensense IIM-42653 6轴IMU
- 两个Pixhawk标准CAN连接器（4针JST GH）
- Pixhawk标准调试连接器（6针JST SH）
- 通过节点参数（CANNODE_TERM）软件控制的内置CAN终端电阻  
- 小型化设计
  - 3cm x 3cm x 1.4cm
- LED指示灯
- 美国制造

## 硬件设置

### 接线

ARK Flow MR通过Pixhawk标准4针JST GH线缆连接到CAN总线。  
更多信息请参阅[CAN接线](../can/index.md#wiring)说明。

### 安装

推荐安装方向为电路板接头指向**机体后部**，如下图所示。

![ARK Flow MR与Pixhawk对齐](../../assets/hardware/sensors/optical_flow/ark_flow_orientation.png)

这对应参数[SENS_FLOW_ROT](../advanced_config/parameter_reference.md#SENS_FLOW_ROT)的默认值（`0`）。  
如果使用不同方向，请相应调整参数。

传感器可安装在机体任何位置，但在[PX4配置](#px4-configuration)期间需要指定相对于机体重心的焦点位置。

## 固件设置

ARK Flow MR运行[PX4 DroneCAN固件](px4_cannode_fw.md)。  
因此支持通过CAN总线进行固件更新和[动态节点分配](index.md#node-id-allocation)。

ARK Flow MR出厂时已预装最新固件，如果需要自行编译和烧录最新固件，请参阅[PX4 DroneCAN固件 > 编译固件](px4_cannode_fw.md#building-the-firmware)。

- 固件目标: `ark_can-flow-mr_default`
- 引导加载程序目标: `ark_can-flow-mr_canbootloader`

## 飞行控制器设置

::: info
如果飞行控制器在上电时没有SD卡，Ark Flow MR将不会启动。
:::

### 启用DroneCAN

步骤如下：

- 在_QGroundControl_中将参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)设置为`2`以启用动态节点分配（或`3`如果使用[DroneCAN电调](../dronecan/escs.md)），然后重启（详见[查找/更新参数](../advanced_config/parameters.md)）。
- 将ARK Flow MR的CAN连接到Pixhawk的CAN接口。

启用后，模块将在启动时被检测到。  
光流数据将以100Hz频率接收。  
距离传感器数据将以40Hz频率接收。

PX4中DroneCAN配置的详细说明请参阅[DroneCAN > 启用DroneCAN](../dronecan/index.md#enabling-dronecan)。

### PX4配置

需要设置EKF光流参数以启用光流测量融合以计算速度，设置必要的[DroneCAN](index.md)参数，并在传感器未居中时定义偏移。

在_QGroundControl_中设置以下参数：

- 通过设置[EKF2_OF_CTRL](../advanced_config/parameter_reference.md#EKF2_OF_CTRL)启用光流融合。
- 可选禁用GPS辅助，将[EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL)设置为`0`。
- 启用[UAVCAN_SUB_FLOW](../advanced_config/parameter_reference.md#UAVCAN_SUB_FLOW)。
- 启用[UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG)。
- 将[EKF2_RNG_A_HMAX](../advanced_config/parameter_reference.md#EKF2_RNG_A_HMAX)设置为`10`。
- 将[EKF2_RNG_QLTY_T](../advanced_config/parameter_reference.md#EKF2_RNG_QLTY_T)设置为`0.2`。
- 将[UAVCAN_RNG_MIN](../advanced_config/parameter_reference.md#UAVCAN_RNG_MIN)设置为`0.08`。
- 将[UAVCAN_RNG_MAX](../advanced_config/parameter_reference.md#UAVCAN_RNG_MAX)设置为`50`。
- 将[SENS_FLOW_MINHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MINHGT)设置为`0.08`。
- 将[SENS_FLOW_MAXHGT](../advanced_config/parameter_reference.md#SENS_FLOW_MAXHGT)设置为`25`。
- 将[SENS_FLOW_MAXR](../advanced_config/parameter_reference.md#SENS_FLOW_MAXR)设置为`7.4`以匹配PAW3902最大角流速。
- 参数[EKF2_OF_POS_X](../advanced_config/parameter_reference.md#EKF2_OF_POS_X)、[EKF2_OF_POS_Y](../advanced_config/parameter_reference.md#EKF2_OF_POS_Y)和[EKF2_OF_POS_Z](../advanced_config/parameter_reference.md#EKF2_OF_POS_Z)用于定义相对于机体重心的偏移。

## ARK Flow MR配置

可通过以下方式配置ARK Flow MR模块：

- 在_QGroundControl_中通过[DroneCAN设备配置](../dronecan/device_configuration.md)界面设置参数。
- 使用DroneCAN终端工具直接发送配置命令。

### 参数参考

下表列出ARK Flow MR的关键配置参数：

| 参数名称         | 描述                  |
|------------------|-----------------------|
| `CANNODE_TERM`   | CAN终端电阻启用/禁用  |
| `FLW_RATE`       | 光流更新频率          |
| `RNG_PWRMODE`     | 距离传感器电源模式    |
| `IMU_ODR`        | IMU输出数据速率       |

完整参数列表请参阅[DroneCAN设备参数参考](../dronecan/device_parameters.md)。

## 故障排除

### LED指示含义

| LED状态          | 含义                  |
|------------------|-----------------------|
| 常亮红色LED       | 严重错误              |
| 闪烁红色LED       | 固件验证失败          |
| 常亮绿色LED       | 正常运行              |
| 闪烁绿色LED       | 正在进行自检          |

### 常见问题

**Q: 模块无法被检测到？**  
A: 检查CAN总线连接和终端电阻设置，确保SD卡已插入飞行控制器。

**Q: 光流数据异常？**  
A: 确保传感器安装表面清洁，检查[EKF2_OF_CTRL]参数配置。

**Q: 距离测量不稳定？**  
A: 尝试调整[RNG_PWRMODE]参数到更高功率模式。