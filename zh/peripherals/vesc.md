# VESC 电调（DroneCAN）

[VESC 项目](https://vesc-project.com/) 是一个完全开源的硬件和软件设计，用于高级 FOC 电机控制器。  
虽然可以通过传统的 PWM 输入进行控制，但它也支持通过 CAN 总线使用 [DroneCAN](../dronecan/index.md) 进行连接。

## 购买渠道

[Vesc 项目 > 硬件](https://vesc-project.com/Hardware)

## 硬件设置

### 接线

电调通过 VESC CAN 接口连接到 CAN 总线。请注意，这不是 Pixhawk 标准的 4 针 JST GH 接口。更多信息请参考 [CAN 接线](../can/index.md#wiring) 指南。电调顺序无关紧要。

## 固件设置

电机编号的首选工具是 [VESC tool](https://vesc-project.com/vesc_tool)。  
除了在 VESC tool 中需要设置的常规电机配置外，还需要正确配置应用设置。  
推荐的应用配置如下：

| 参数                     | 选项                 |
| ------------------------ | -------------------- |
| 应用选择                 | `No App`           |
| VESC ID                  | `1,2,...`          |
| CAN 状态消息模式         | `CAN_STATUS_1_2_3_4_5` |
| CAN 波特率               | `CAN_BAUD_500K`      |
| CAN 模式                 | `UAVCAN`           |
| UAVCAN 电调索引          | `0,1,...`          |

VESC ID 应与 PX4 命名规则中的电机编号一致，从右上方电机 `1`、左下方电机 `2` 等开始编号。  
但 UAVCAN 电调索引从 `0` 开始，因此始终比 VESC ID 低一个索引。  
例如，在四旋翼中，左下方电机将具有 `VESC ID = 2` 和 `UAVCAN 电调索引 = 1`。

最后，CAN 波特率必须与 [UAVCAN_BITRATE](../advanced_config/parameter_reference.md#UAVCAN_BITRATE) 中设置的值匹配。

## 飞控设置

### 启用 DroneCAN

将电调连接到 Pixhawk 的 CAN 总线。使用电池或电源供电（而非仅通过 USB 为飞控供电）启动整个机体，并通过将参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 设置为 `3` 来启用 DroneCAN 驱动程序，以同时启用动态节点 ID 分配和 DroneCAN 电调输出。

### PX4 配置

使用 [执行器](../config/actuators.md#actuator-testing) 配置界面将电机分配到输出端口。

<!-- 由于链接文档中没有相关信息已移除 -->
<!--
## 故障排查

查看 DroneCAN 故障排查 - (index.md#troubleshooting)
-->

## 更多信息

- [VESC 项目电调](https://vesc-project.com/)
- [Benjamin Vedder 的博客](http://vedder.se)（项目负责人）