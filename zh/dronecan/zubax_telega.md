# Zubax Telega 电调

Zubax Telega 是一种高端的无传感器FOC电机控制技术。  
该技术被应用于多种产品中，包括 [Zubax Myxa](https://zubax.com/products/myxa) 电调、[Zubax Mitochondrik](https://zubax.com/products/mitochondrik) 电机控制器模块以及 Zubax Sadulli 集成驱动器。

虽然 Telega 可以通过传统的PWM输入进行控制，但其设计目标是通过CAN总线使用 [DroneCAN](index.md) 进行操作。

::: info
基于 Zubax Telega 的电调需要进行非平凡的推进系统调校，以实现良好的性能并确保稳定运行。  
缺乏必要调校经验的用户建议购买 [预调校的无人机推进套件](https://zubax.com/products/uav_propulsion_kits) 或使用 Zubax 机器人公司的专业调校服务。  
关于此问题的疑问请发送至：[support@zubax.com](mailto:support@zubax.com)。
:::

![Sadulli - Top](../../assets/peripherals/esc_usavcan_zubax_sadulli/sadulli_top.jpg)

## 购买渠道

- [Zubax Myxa](https://zubax.com/products/myxa)：面向轻型无人机和水下机器人的高端PMSM/BLDC电机控制器（FOC电调）
- [Zubax Mitochondrik](https://zubax.com/products/mitochondrik)：集成式无传感器PMSM/BLDC电机控制器芯片（用于电调和集成驱动器）
- [Zubax Komar](https://shop.zubax.com/products/komar-motor-controller-open-hardware-reference-design-for-mitochondrik?variant=32931555868771)：Mitochondrik的开源硬件参考设计
- [Zubax Sadulli 集成驱动器](https://shop.zubax.com/collections/integrated-drives/products/sadulli-integrated-drive-open-hardware-reference-design-for-mitochondrik?variant=27740841181283)

## 硬件设置

电调通过Pixhawk标准的4针JST GH线缆连接到CAN总线。  
更多详情请参阅 [CAN接线指南](../can/index.md#wiring)。电调顺序无关紧要。

## 固件设置

[基于Telega的电调](https://zubax.com/products/telega) 的电机编号通常通过 [Kucher工具](https://files.zubax.com/products/com.zubax.kucher/) 进行（或使用较不"GUI友好"的 [DroneCAN GUI工具](https://dronecan.github.io/GUI_Tool/Overview/)）。  
Telega 不支持通过电机旋转进行自动编号。

相关指导文档：[Myxa v0.1快速入门指南](https://forum.zubax.com/t/quick-start-guide-for-myxa-v0-1/911)（Zubax博客）

Telega电调还需要其他电机设置和配置以实现可靠性能。详见上述指南及其他Zubax文档。

## 飞控设置

### 启用DroneCAN

将电调连接到Pixhawk的CAN总线。通过电池或电源（而非仅通过USB为飞控供电）上电整个机体，并通过将参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 设置为 `3` 来启用DroneCAN驱动器，该设置同时启用动态节点ID分配和DroneCAN电调输出。

### PX4配置

通过 [执行器](../config/actuators.md#actuator-testing) 配置界面将电机分配到输出端口。

## 故障排查

### 武器状态正常但电机不转

如果PX4固件已武装但电机未开始旋转，请检查参数 `UAVCAN_ENABLE=3` 是否已启用DroneCAN电调。  
如果在增加推力前电机未开始旋转，请使用 [执行器 > 执行器测试](../config/actuators.md#actuator-testing) 确认电机输出已设置为正确的最小值。

### DroneCAN设备无法分配节点ID/固件更新失败

PX4在DroneCAN节点分配和固件更新（启动时发生）过程中需要SD卡。  
请检查SD卡是否已插入（且正常工作），然后重新启动。