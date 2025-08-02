

# RaccoonLab FMUv6X 自动驾驶仪

:::warning
PX4 不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://raccoonlab.co)。
:::

[RaccoonLab FMUv6X](https://docs.raccoonlab.co/guide/autopilot/RCLv6X.html) 飞行控制器基于以下 Pixhawk® 标准：[Pixhawk Autopilot FMUv6X Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)，[Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 和 [Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

配备高性能 H7 处理器、模块化设计、三重冗余、温度控制 IMU 板和隔离传感器域，其性能、可靠性和灵活性表现出色。在 RaccoonLab，我们专注于基于 DroneCAN 和 Cyphal 的机载控制系统总线。我们的自动驾驶仪是更大 DroneCAN 和 Cyphal 生态系统的一部分，是下一代智能载体的理想选择。

![RaccoonLab FMUv6X](../../assets/flight_controller/raccoonlab/fmuv6x.png)

RaccoonLab 为 Raspberry Pi 和 NVIDIA Jetson Xavier NX 提供多功能 HAT，增强连接性和功能。[Jetson Xavier NX HAT](https://docs.raccoonlab.co/guide/nx_hat/) 旨在将 CAN 总线与 Jetson Xavier NX 集成，实现对 Cyphal 和 DroneCAN 协议的访问。[Raspberry Pi CM4 HAT](https://docs.raccoonlab.co/guide/rpi_hat/) 提供强大功能，包括 CAN 总线连接、LTE 调制解调器、内部电压测量、其他 MCU 的 SWD 调试以及通过 MAVLINK 与 PX4 的 UART 通信。这些 HAT 扩展了设备功能，使其成为高级机器人和无人机应用的理想选择。

:::tip
此自动驾驶仪受到 PX4 维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 关键设计点

- 高性能STM32H753处理器  
- 模块化飞行控制器：分离的IMU、FMU和基板系统通过100针和50针Pixhawk Autopilot Bus连接器连接  
- 冗余设计：3个IMU传感器和2个气压计传感器位于独立总线上  
- 三重冗余域：完全隔离的传感器域，具有独立总线和独立电源控制  
- 新型振动隔离系统，可过滤高频振动并降低噪声以确保读数精度  
- 以太网接口用于高速任务计算机集成

## 处理器与传感器

- FMU处理器：STM32H753
  - 32位Arm Cortex-M7，480MHz，2MB闪存，1MB RAM
- IO处理器：STM32F100
  - 32位Arm Cortex-M3，24MHz，8KB SRAM
- 机载传感器
  - 加速度计/陀螺仪：ICM-20649 或 BMI088
  - 加速度计/陀螺仪：ICM-42688-P
  - 加速度计/陀螺仪：ICM-42670-P
  - 磁力计：BMM150
  - 气压计：2x BMP388

## 电气数据

- 电压规格：
  - 最大输入电压：36V
  - USB供电输入：4.75\~5.25V
  - 舵机电轨输入：0\~36V
- 电流规格：
  - `TELEM1` 输出电流限制器：1.5A
  - 其他所有端口合计输出电流限制器：1.5A

## 机械数据

- 尺寸
  - 飞控模块：38.8 x 31.8 x 14.6mm
  - 标准底板：52.4 x 103.4 x 16.7mm
  - 迷你底板：43.4 x 72.8 x 14.2 mm
- 重量
  - 飞控模块：23g
  - 标准底板：51g
  - 迷你底板：26.5g

3D模型可从[GrabCAD](https://grabcad.com/library/raccoonlab-autopilot-1)下载。

![RaccoonLab FMUv6X drawings](../../assets/flight_controller/raccoonlab/fmuv6x-drw.png)

## 接口

- 16路PWM舵机输出
- 支持Spektrum/DSM的遥控输入
- 专用PPM和S.Bus输入的遥控输入
- 专用模拟/PWM RSSI输入和S.Bus输出
- 4个通用串口
  - 3个带完整流控
  - 1个带独立1.5A电流限制(`TELEM1`)
  - 1个带I2C和额外GPIO线的外部NFC读卡器
- 2个GPS端口
  - 1个完整GPS+安全开关端口
  - 1个基础GPS端口
- 1个I2C端口
- 1个以太网端口
  - 无变压器设计
  - 100Mbps速率
- 1个SPI总线
  - 2条片选线
  - 2条数据准备好线
  - 1条SPI同步线
  - 1条SPI复位线
- 2条CAN总线用于CAN外设
  - CAN总线支持独立静默控制或电调RX-MUX控制
- 2个带SMBus的电源输入口
  - 1个AD&IO端口
  - 2个额外模拟输入
  - 1个PWM/捕获输入
  - 2条专用调试和GPIO线

## 串口映射

| UART   | 设备         | 端口            |
| ------ | ------------ | --------------- |
| USART1 | /dev/ttyS0   | GPS           |
| USART2 | /dev/ttyS1   | TELEM3        |
| USART3 | /dev/ttyS2   | 调试控制台     |
| UART4  | /dev/ttyS3   | UART4 & I2C   |
| UART5  | /dev/ttyS4   | TELEM2        |
| USART6 | /dev/ttyS5   | PX4IO/RC      |
| UART7  | /dev/ttyS6   | TELEM1        |
| UART8  | /dev/ttyS7   | GPS2          |

## 电压等级

_RaccoonLab FMUv6X_ 可在供电方面实现三重冗余（需提供三个电源）。  
三个电源轨分别为：**POWER1**、**POWER2** 和 **USB**。  
RaccoonLab FMUv6X 上的 **POWER1** 和 **POWER2** 接口采用[2.00mm Pitch CLIK-Mate Wire-to-Board PCB Receptacle](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670)（6电路）接口。

**正常运行最大电压等级**

在以下条件下，系统将按以下顺序使用所有电源供电：  
1. **POWER1** 和 **POWER2** 输入（4.9V 到 5.5V）  
2. **USB** 输入（4.75V 到 5.25V）  

:::tip  
制造商 [RaccoonLab Docs](https://docs.raccoonlab.co/guide/autopilot/RCLv6X.html) 是 RaccoonLab FMUv6X 自主飞行器的权威参考文档。  
建议优先使用该文档，因其包含最完整且最新的信息。  
:::

## 购买地点

[RaccoonLab 商店](https://raccoonlab.co/store)

[Cyphal 商店](https://cyphal.store)

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接了合适的硬件时，QGroundControl 会预先构建并自动安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)以用于此目标：

```sh
make px4_fmu-v6x_default
```

## 支持的平台/机架

支持使用普通遥控舵机或Futaba S-Bus舵机控制的多旋翼、固定翼飞机、地面探测车或船只。完整的支持配置列表可在[机架参考](../airframes/airframe_reference.md)中查看。

## 进一步信息

- [Pixhawk 自动驾驶FMUv6X标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)
- [Pixhawk 自动驾驶总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)
- [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawke%20Connector%20Standard.pdf)
- [RaccoonLab 文档](http://docs.raccoonlab.co)