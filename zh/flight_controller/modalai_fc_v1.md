

# ModalAI 飞行核心 v1

<Badge type="tip" text="PX4 v1.11" />

:::warning
PX4不制造此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://forum.modalai.com/)。
:::

[ModalAI Flight Core v1](https://modalai.com/flight-core) ([数据手册](https://docs.modalai.com/flight-core-datasheet)) 是一款在美国制造的PX4飞控。
Flight Core 可与 ModalAI [VOXL](https://modalai.com/voxl) ([数据手册](https://docs.modalai.com/voxl-datasheet/)) 配合使用，实现避障和无GPS导航功能，也可作为独立飞控使用。

![FlightCoreV1](../../assets/flight_controller/modalai/fc_v1/main.jpg)

Flight Core 与 [VOXL Flight](https://www.modalai.com/voxl-flight) ([数据手册](https://docs.modalai.com/voxl-flight-datasheet/)) 中的PX4飞控部分完全相同，VOXL Flight将VOXL协处理器和Flight Core集成在单一PCB上。

::: info
此飞控为[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的飞控。
:::

## 规格

| 功能             | 详细信息                                                          |
| :--------------- | :--------------------------------------------------------------- |
| 重量             | 6 克                                                              |
| 主控芯片         | 216MHz，32位ARM M7 [STM32F765II][stm32f765ii]                 |
| 内存             | 256Kb FRAM                                                       |
|                  | 2Mbit Flash                                                      |
|                  | 512Kbit SRAM                                                     |
| 固件             | [PX4][px4]                                                       |
| 惯性测量单元     | [ICM-20602][icm-20602] (SPI1)                                    |
|                  | ICM-42688 (SPI2)                                                 |
|                  | [BMI088][bmi088] (SPI6)                                          |
| 气压计           | [BMP388][bmp388] (I2C4)                                          |
| 安全元件         | [A71CH][a71ch] (I2C4)                                            |
| microSD 卡       | [支持的存储卡信息](../dev_log/logging.md#sd-cards)             |
| 输入接口         | GPS/磁力计                                                       |
|                  | Spektrum                                                         |
|                  | 遥测                                                             |
|                  | CAN总线                                                          |
|                  | PPM                                                              |
| 输出接口         | 6 LED (2xRGB)                                                    |
|                  | 8 PWM通道                                                        |
| 额外接口         | 3串口                                                            |
|                  | I2C                                                              |
|                  | GPIO                                                             |

::: info
更详细的硬件文档可以在此处找到 [here](https://docs.modalai.com/flight-core-datasheet/)。
:::

<!-- 表格引用链接（优化布局） -->
[stm32f765ii]: https://www.st.com/en/microcontrollers-microprocessors/stm32f765ii.html
[bmp388]: https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp388/
[icm-20602]: https://www.invensense.com/products/motion-tracking/6-axis/icm-20602/
[bmi088]: https://www.bosch-sensortec.com/bst/products/all_products/bmi088_1
[px4]: https://github.com/PX4/PX4-Autopilot/tree/main/boards/modalai/fc-v1
[a71ch]: https://www.nxp.com/products/security-and-authentication/authentication/plug-and-trust-the-fast-easy-way-to-deploy-secure-iot-connections:A71CH

## 尺寸

![FlightCoreV1Dimensions](../../assets/flight_controller/modalai/fc_v1/dimensions.png)

## PX4 固件兼容性

_Flight Core v1_ 完全兼容官方 PX4 固件（从 PX4 v1.11 开始）。

ModalAI 为 PX4 v1.11 维护一个 [分支版 PX4](https://github.com/modalai/px4-firmware/tree/modalai-1.11)。
这包括 UART ESC 支持，以及计划合并到上游的 VIO 和 VOA 改进。

有关固件的更多信息，请参见[此处](https://docs.modalai.com/flight-core-firmware/)。

## QGroundControl 支持

该开发板在 QGroundControl 4.0 及更高版本中提供支持。

## 可用性

- [Flight Core 完整套件](https://modalai.com/flight-core)
- [集成 VOXL 伴生计算机的 Flight Core 单 PCB 版本](https://modalai.com/flight-core)
- [集成 VOXL 伴生计算机和避障摄像头的 Flight Core (VOXL Flight Deck)](https://modalai.com/flight-deck) ([数据手册](https://docs.modalai.com/voxl-flight-deck-platform-datasheet/))
- [集成 VOXL 和摄像头的 Flight Core 组装套件](https://shop.modalai.com/products/voxl-flight-deck-r1)

## 快速入门

### 方向

下图展示了推荐的朝向，该朝向自PX4 v1.11版本起对应于`ROTATION_NONE`。

![FlightCoreV1Orientation](../../assets/flight_controller/modalai/fc_v1/orientation.png)

### 连接器

关于引脚定义的详细信息请参考[此处](https://docs.modalai.com/flight-core-datasheet-connectors)。

![FlightCoreV1Top](../../assets/flight_controller/modalai/fc_v1/top.png)

| 连接器 | 概述                                                    |
| ------- | --------------------------------------------------------- |
| J1      | VOXL通信接口连接器（TELEM2）                             |
| J2      | 编程与调试连接器                                         |
| J3      | USB连接器                                                |
| J4      | UART2, UART电调（TELEM3）                                |
| J5      | 通信连接器（TELEM1）                                     |
| J6      | VOXL-电源管理输入/扩展                                   |
| J7      | 8通道PWM输出连接器                                       |
| J8      | CAN总线连接器                                            |
| J9      | PPM遥控输入                                              |
| J10     | 外部GPS & 磁力计连接器                                   |
| J12     | 遥控输入，Spektrum/SBus/UART连接器                       |
| J13     | I2C显示（备用传感器连接器）/安全按钮输入                |

![FlightCoreV1Bottom](../../assets/flight_controller/modalai/fc_v1/bottom.png)

### 用户指南

完整的用户指南可在此处获取 [https://docs.modalai.com/flight-core-manual/](https://docs.modalai.com/flight-core-manual/)

### 如何构建

要[构建 PX4](../dev_setup/building_px4.md)以适配该目标：

```
make modalai_fc-v1
```

## 串口映射

| UART   | 设备       | 端口                                     |
| ------ | ---------- | ---------------------------------------- |
| USART1 | /dev/ttyS0 | GPS1 (J10)                               |
| USART2 | /dev/ttyS1 | TELEM3 (J4)                              |
| USART3 | /dev/ttyS2 | 调试控制台 (J2)                          |
| UART4  | /dev/ttyS3 | 扩展UART (J6)                            |
| UART5  | /dev/ttyS4 | TELEM2，主VOXL通信 (J1)                  |
| USART6 | /dev/ttyS5 | 遥控器 (J12)                             |
| UART7  | /dev/ttyS6 | TELEM1 (J5)                              |
| UART8  | /dev/ttyS7 | N/A                                      |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

## 支持

请访问[ModalAI论坛](https://forum.modalai.com/category/10/flight-core)以获取更多信息。