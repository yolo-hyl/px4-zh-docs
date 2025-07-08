# Aion Robotics R1 UGV

<Badge type="tip" text="PX4 v1.15" />

[Aion R1](https://www.aionrobotics.com/) 机体被选中用于测试并改进PX4的差速驱动支持，同时优化对Roboclaw电机控制器的驱动支持，例如 [RoboClaw 2x15A](https://www.basicmicro.com/RoboClaw-2x15A-Motor-Controller_p_10.html)。

此处的文档和驱动信息也有助于在其他机体上使用Roboclaw控制器，以及在类似[Aion R6](https://www.aionrobotics.com/r6)的机体上进行操作。

目前，PX4支持此配置的手动模式。

![Aion Robotics R1 UGV](../../assets/airframes/rover/aion_r1/r1_rover_no_bg.png)

## 部件清单

- [Aion R1 (已停产)](https://www.aionrobotics.com/)
  - [文档](https://github-docs.readthedocs.io/en/latest/r1-ugv.html)
- [RoboClaw 2x15A](https://www.basicmicro.com/RoboClaw-2x15A-Motor-Controller_p_10.html)
  - [R1 Roboclaw 规格](https://resources.basicmicro.com/aion-robotics-r1-autonomous-robot/)
- [Auterion Skynode](../companion_computer/auterion_skynode.md)

## 组装

组装包括一个3D打印的框架，所有自动驾驶仪部件均安装在该框架上。
此构建包含一个[Auterion Skynode](../companion_computer/auterion_skynode.md)，通过Pixhawk适配板连接，该适配板通过串口与RoboClaw电机控制器通信。

![R1 组装](../../assets/airframes/rover/aion_r1/r1_assembly.png)

::: info
如果使用标准Pixhawk，可以将RoboClaw直接连接到飞控，无需适配板。
:::

RoboClaw应连接到飞控上合适的串口（UART）端口，如 `GPS2` 或 `TELEM1`。
其他RoboClaw接线详情请参阅[RoboClaw用户手册](https://downloads.basicmicro.com/docs/roboclaw_user_manual.pdf)的“Packet Serial Wiring”部分，如下图所示（此配置已验证兼容性）。

![串口接线编码器](../../assets/airframes/rover/aion_r1/wiring_r1.jpg)

## PX4配置

### 机体配置

使用 _QGroundControl_ 进行机体配置：

1. 在 [基础配置](../config/index.md) 部分，选择 [机体](../config/airframe.md) 选项卡。
1. 在 **Rover** 分类下选择 **Aion Robotics R1 UGV**。

![选择机体](../../assets/airframes/rover/aion_r1/r1_airframe.png)

### RoboClaw 配置

首先配置串口连接：

1. 在 QGroundControl 中导航到 [参数](../advanced_config/parameters.md) 部分。

   - 将 [RBCLW_SER_CFG](../advanced_config/parameter_reference.md#RBCLW_SER_CFG) 参数设置为RoboClaw连接的串口（如 `GPS2`）。
   - [RBCLW_COUNTS_REV](../advanced_config/parameter_reference.md#RBCLW_COUNTS_REV) 指定一个轮子旋转所需的编码器计数。
     对于已测试的 `RoboClaw 2x15A Motor Controller`，此值应保留为 `1200`。
     根据您的具体编码器和轮子设置调整该值。
   - RoboClaw 电机控制器必须在总线上分配唯一地址。
     默认地址为 128，无需更改（如果更改，请更新 PX4 参数 [RBCLW_ADDRESS](../advanced_config/parameter_reference.md#RBCLW_ADDRESS) 以匹配）。

     ::: info
     PX4 不支持同一机体中使用多个 RoboClaw 电机控制器——每个控制器需要在总线上有唯一地址，而 PX4 中仅有一个地址设置参数（`RBCLW_ADDRESS`）。
     :::

然后配置执行器配置：

1. 在 QGroundControl 中导航到 [执行器配置与测试](../config/actuators.md)。
1. 从 _执行器输出_ 列表中选择 RoboClaw 驱动。

   有关通道分配、最小值和最大值，请参考下图。

   ![RoboClaw QGC](../../assets/airframes/rover/aion_r1/roboclaw_actuator_config_qgc.png)

   对于拥有超过两个电机的系统，可以将相同功能分配给多个电机。
   不寻常值的原因可在 [RoboClaw 用户手册](https://downloads.basicmicro.com/docs/roboclaw_user_manual.pdf) 的 `Compatibility Commands` 部分找到（针对 `Packet Serial`）：

   ```plain
   Drive motor forward. Valid data range is 0 - 127. A value of 127 = full speed forward, 64 =
   about half speed forward and 0 = full stop.
   ```

## 参见

- [roboclaw](../modules/modules_driver.md#roboclaw) 驱动
- [Roboclaw 用户手册](https://downloads.basicmicro.com/docs/roboclaw_user_manual.pdf)