# ARK CANnode

[ARK CANnode](https://arkelectron.com/product/ark-cannode/) 是一个开源通用[DroneCAN](../dronecan/index.md)节点，包含6自由度IMU。  
其主要目的是使非CAN传感器（I2C、SPI、UART）能够在CAN总线上使用。  
它还具有PWM输出，可扩展机体的控制输出数量和物理距离。

![ARK CANnode](../../assets/hardware/can_nodes/ark_cannode.jpg)

## 购买渠道

从以下渠道订购该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-cannode/) (美国)

## 硬件规格

- [开源原理图和物料清单](https://github.com/ARK-Electronics/ARK_CANNODE)
- 传感器
  - Bosch BMI088 6轴IMU 或 Invensense ICM-42688-P 6轴IMU
- STM32F412CGU6 MCU
  - 1MB Flash
- 两个Pixhawk标准CAN接口
  - 4针JST GH
- Pixhawk标准I2C接口
  - 4针JST GH
- Pixhawk标准UART/I2C接口（基础GPS端口）
  - 6针JST GH
- Pixhawk标准SPI接口
  - 7针JST GH
- PWM接口
  - 10针JST JST
  - 8路PWM输出
  - 与Pixhawk 4 PWM接口引脚兼容
- Pixhawk标准调试接口
  - 6针JST SH
- 小尺寸
  - 3cm x 3cm x 1.3cm
- LED指示灯
- 美国制造
- 电源需求
  - 5V
  - 电流消耗取决于连接的外设

## 硬件设置

### 接线

ARK CANnode通过Pixhawk标准4针JST GH电缆连接到CAN总线。  
更多信息请参见[CAN接线](../can/index.md#wiring)说明。

## 固件设置

ARK CANnode运行[PX4 DroneCAN固件](px4_cannode_fw.md)。  
因此支持通过CAN总线进行固件更新和[动态节点分配](index.md#node-id-allocation)。

ARK CANnode出厂时已预装最新固件，但若需要自行构建和烧录最新固件，请参考[PX4 DroneCAN固件 > 构建固件](px4_cannode_fw.md#building-the-firmware)。

- 固件目标：`ark_cannode_default`
- 引导程序目标：`ark_cannode_canbootloader`

## 飞行控制器配置

### 启用DroneCAN

使用ARK CANnode板时，需要将其连接到Pixhawk的CAN总线，并通过设置参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)为`2`启用DroneCAN驱动（动态节点分配），或设置为`3`（若使用[DroneCAN电调](../dronecan/escs.md)）。

操作步骤如下：

- 在_QGroundControl_中设置参数[UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE)为`2`或`3`并重启（详见[查找/更新参数](../advanced_config/parameters.md)）。
- 将ARK CANnode的CAN接口连接到Pixhawk的CAN总线。

启用后，模块将在启动时被检测到。

关于PX4中DroneCAN配置的更多细节，请参见[DroneCAN > 启用DroneCAN](../dronecan/index.md#enabling-dronecan)。

### 启用传感器

需要为连接到ARK CANnode的每个传感器启用相应的订阅器。

这通过参数参考中的`UAVCAN_SUB_*`参数完成（例如[UAVCAN_SUB_ASPD](../advanced_config/parameter_reference.md#UAVCAN_SUB_ASPD)、[UAVCAN_SUB_BARO](../advanced_config/parameter_reference.md#UAVCAN_SUB_BARO)等）。

## Ark CANNode配置

在ARK CANnode上，可能需要配置以下参数：

| 参数                                                                                       | 描述                   |
| ----------------------------------------------------------------------------------------------- | ----------------------------- |
| <a id="CANNODE_TERM"></a>[CANNODE_TERM](../advanced_config/parameter_reference.md#CANNODE_TERM) | CAN内置总线端接。 |

## LED含义

在烧录时，ARK CANnode上会看到红蓝LED；运行正常时蓝色LED常亮。

若看到红色LED常亮表示出现错误，请检查以下内容：

- 确保飞行控制器已安装SD卡。
- 确保在烧录`ark_cannode_default`之前已安装`ark_cannode_canbootloader`。
- 清除SD卡根目录和ufw目录中的二进制文件，重新构建和烧录。

## 参见

- [ARK CANnode 文档](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-cannode) (ARK Docs)