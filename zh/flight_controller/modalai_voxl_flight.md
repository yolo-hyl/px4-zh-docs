

# ModalAI VOXL飞行

<Badge type="tip" text="PX4 v1.11" />

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://forum.modalai.com/)。
:::

ModalAI [VOXL Flight](https://modalai.com/voxl-flight) ([数据手册](https://docs.modalai.com/voxl-flight-datasheet)) 是首批将Snapdragon的高性能与复杂性，以及在STM32F7上PX4的灵活性和易用性相结合的计算平台之一。在美国制造的VOXL飞行支持避障和基于PX4飞控的无GPS（室内）导航，所有功能集成在单块PCB上。

![VOXL-Flight](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-dk.jpg)

::: info
此飞控模块获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 规格

### 系统

| 功能    | 详情   |
| :------ | :----- |
| 重量    | 26 g   |

### 伴随计算机

| 功能               | 详情                                                                                                                                                                                                                                                            |
| :----------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 基础操作系统       | Linux Yocto Jethro 3.18内核。可通过VOXL运行Docker使用其他Linux操作系统，详情[此处](https://docs.modalai.com/docker-on-voxl/)                                                                                            |
| 计算能力           | 高通骁龙821配4GB LPDDR4 1866MHz，骁龙821 [数据手册](https://developer.qualcomm.com/download/sd820e/qualcomm-snapdragon-820e-processor-apq8096sge-device-specification.pdf)，[文档](https://developer.qualcomm.com/hardware/apq-8096sg/tools) |
| CPU                | 四核CPU最高可达2.15GHz                                                                                                                                                                                                                                        |
| GPU                | Adreno 530 GPU 624MHz                                                                                                                                                                                                                                           |
| 计算DSP            | Hexagon计算DSP(cDSP) 825MHz                                                                                                                                                                                                                                  |
| 传感器DSP          | Hexagon传感器DSP(sDSP) 700MHz                                                                                                                                                                                                                                   |
| 视频               | 4k30视频捕获h.264/5带720p FPV                                                                                                                                                                                                                             |
| 摄像头接口         | 支持MIPI-CSI2、USB UVC、HDMI                                                                                                                                                                                                                               |
| Wi-Fi              | 预认证Wi-Fi模块 [FCC ID:PPD-QCNFA324](https://fccid.io/PPD-QCNFA324)，QCA6174A调制解调器，802.11ac 2x2双频，蓝牙4.2（双模式）                                                                                                        |
| 4G LTE             | [可选扩展模块](https://www.modalai.com/collections/voxl-add-ons/products/voxl-lte)                                                                                                                                                                       |
| Microhard pDDL     | [可选扩展模块](https://www.modalai.com/collections/voxl-add-ons/products/voxl-microhard-modem-usb-hub)                                                                                                                                                   |
| GNSS               | WGR7640 10Hz                                                                                                                                                                                                                                                       |
| I/O                | 1x USB3.0 OTG(ADB端口)，1x USB2.0(扩展端口)，2x UART，3x I2C，可配置额外GPIO和SPI                                                                                                                                                   |
| 存储               | 32GB(UFS 2.0)，Micro SD卡                                                                                                                                                                                                                                      |
| 软件               | Docker、OpenCV 2.4.11、3.4.6、4.2、ROS Indigo、高通机器视觉SDK，详见[GitLab](https://gitlab.com/voxl-public)获取大量开源示例！                                                                                                         |
| IMU                | ICM-42688(SPI10)，ICM-20948(SPI1)                                                                                                                                                                                                                                |
| 气压计             | BMP280                                                                                                                                                                                                                                                             |

### 飞行控制器

| 特性             | 详情                                                                 |
| :--------------- | :------------------------------------------------------------------ |
| 微控制器 (MCU)   | 216MHz，32位ARM M7 [STM32F765II][stm32f765ii]                        |
| 存储             | 256Kb FRAM                                                           |
|                  | 2Mbit Flash                                                          |
|                  | 512Kbit SRAM                                                         |
| 固件             | [PX4][px4]                                                           |
| 惯性测量单元 (IMUs) | [ICM-20602][icm-20602] (SPI1)                                       |
|                  | ICM-42688 (SPI2)                                                     |
|                  | [BMI088][bmi088] (SPI6)                                              |
| 气压计           | [BMP388][bmp388] (I2C4)                                              |
| 安全元件         | [A71CH][a71ch] (I2C4)                                                |
| microSD卡        | [支持卡的信息](../dev_log/logging.md#sd-cards)                     |
| 输入             | GPS/磁力计                                                           |
|                  | Spektrum                                                           |
|                  | 通信遥测                                                           |
|                  | CAN总线                                                            |
|                  | PPM                                                                |
| 输出             | 6个LED (2xRGB)                                                      |
|                  | 8个PWM通道                                                         |
| 额外接口         | 3个串口                                                             |
|                  | I2C                                                                  |
|                  | GPIO                                                                 |

<!-- 上表参考链接（优化布局） -->

[stm32f765ii]: https://www.st.com/en/microcontrollers-microprocessors/stm32f765ii.html
[px4]: https://github.com/PX4/PX4-Autopilot/tree/main/boards/modalai/fc-v1
[icm-20602]: https://www.invensense.com/products/motion-tracking/6-axis/icm-20602/
[bmi088]: https://www.bosch-sensortec.com/bst/products/all_products/bmi088_1
[bmp388]: https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp388/
[a71ch]: https://www.nxp.com/products/security-and-authentication/authentication/plug-and-trust-the-fast-easy-way-to-deploy-secure-iot-connections:A71CH

::: info
更详细的硬件文档请见[此处](https://docs.modalai.com/voxl-flight-datasheet/)。
:::

## 尺寸

![FlightCoreV1Dimensions](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-dimensions.jpg)

[3D STEP 文件](https://storage.googleapis.com/modalai_public/modal_drawings/M0019_VOXL-Flight.zip)

## PX4 固件兼容性

_VOXL Flight_ 完全兼容来自 PX4 的官方 PX4 固件（从 PX4 v1.11 开始）。

ModalAI 为 PX4 v1.11 维护了一个 [分支的 PX4 版本](https://github.com/modalai/px4-firmware/tree/modalai-1.11)。
该版本包括对 UART 电调的支持以及 VIO 和 VOA 的改进，计划将这些改进上传到上游。

更多关于固件的信息可以在这里找到 [here](https://docs.modalai.com/flight-core-firmware/)。

## QGroundControl 支持

此板从 QGroundControl 4.0 及更高版本开始支持。

## 可用性

- [VOXL Flight完整套件](https://modalai.com/voxl-flight)
- [VOXL Flight板](https://www.modalai.com/products/voxl-flight?variant=31707275362355)（仅限）
- [集成避障摄像头的VOXL Flight（VOXL Flight Deck）](https://modalai.com/flight-deck) ([数据手册](https://docs.modalai.com/voxl-flight-deck-platform-datasheet/))
- [集成在即飞VOXL m500开发无人机中的VOXL Flight](https://www.modalai.com/collections/development-drones/products/voxl-m500) ([数据手册](https://docs.modalai.com/voxl-m500-reference-drone-datasheet/))

## 快速入门

厂商提供的快速入门指南位于 [here](https://docs.modalai.com/voxl-flight-quickstart/)。

### voxl-vision-px4

VOXL Flight 在硬件的协同计算机部分运行 [voxl-vision-px4](https://gitlab.com/voxl-public/modal-pipe-architecture/voxl-vision-px4) ，这充当了某种 MAVLink 代理。  
有关详细信息，源代码可在此处获取 [here](https://gitlab.com/voxl-public/modal-pipe-architecture/voxl-vision-px4)

### 连接器

有关引脚分配的详细信息，请参见[此处](https://docs.modalai.com/voxl-flight-datasheet-connectors/)。

#### 顶部视图

![VOXLFlightTop](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-top.jpg)

_Note: 1000系列连接器可通过STM32/PX4访问_

| 连接器    | 功能概述                              | 使用方                  |
| --------- | ------------------------------------- | ----------------------- |
| J2        | 高分辨率4K图像传感器（CSI0）         | Snapdragon - Linux      |
| J3        | 立体图像传感器（CSI1）               | Snapdragon - Linux      |
| J6        | 散热风扇连接器                        | Snapdragon - Linux      |
| J7        | BLSP6（GPIO）和BLSP9（UART）         | Snapdragon - Linux      |
| J13       | 扩展B2B接口                           | Snapdragon - Linux      |
| J14       | 集成GNSS天线连接                      | Snapdragon - Linux      |
| J1001     | 编程与调试/UART3                      | STM32 - PX4            |
| J1002     | 电调串口/TELEM3                       | STM32 - PX4            |
| J1003     | PPM遥控输入                           | STM32 - PX4            |
| J1004     | 遥控输入（Spektrum/SBus/UART6）      | STM32 - PX4            |
| J1006     | USB 2.0接口（PX4/QGroundControl）     | STM32 - PX4            |
| J1007     | 8通道PWM/DShot输出                    | STM32 - PX4            |
| J1008     | CAN总线                               | STM32 - PX4            |
| J1009     | I2C3, UART4                           | STM32 - PX4            |
| J1010     | 通信链路（TELEM1）                    | STM32 - PX4            |
| J1011     | I2C2, 安全按钮输入                     | STM32 - PX4            |
| J1012     | 外部GPS与磁力计，UART1, I2C1         | STM32 - PX4            |
| J1013     | 电源输入, I2C3                        | STM32 - PX4（为整个系统供电） |

#### 底部

![VOXLFlightBottom](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-bottom.jpg)

_注释：1000系列连接器可通过STM32/PX4访问_

| 连接器      | 摘要                                 | 使用方                     |
| -------------- | --------------------------------------- | --------------------------- |
| J4             | 跟踪/光流图像传感器 (CSI2)             | Snapdragon - Linux          |
| J8             | USB 3.0 OTG                             | Snapdragon - Linux, **adb** |
| J10            | BLSP7 UART和I2C板外接口                | Snapdragon - Linux          |
| J11            | BLSP12 UART和I2C板外接口               | Snapdragon - Linux          |
| VOXL microSD   |                                         | Snapdragon - Linux          |
| PX4 microSD    | 32GB最大                                | STM32 - PX4                 |
| Wi-Fi天线      | 已包含                                  | Snapdragon - Linux          |

### 用户指南

完整的用户指南可在此获取 [here](https://docs.modalai.com/voxl-flight-quickstart).

### 如何构建

要[构建 PX4](../dev_setup/building_px4.md)以针对此目标：

```
make modalai_fc-v1
```

## 串口映射

_注意：此处显示的映射仅适用于由 PX4 控制的接口_

| UART   | 设备           | 端口                                     |
| ------ | -------------- | ---------------------------------------- |
| USART1 | /dev/ttyS0     | GPS1 (J1012)                             |
| USART2 | /dev/ttyS1     | TELEM3 (J1002)                           |
| USART3 | /dev/ttyS2     | 调试控制台 (J1001)                       |
| UART4  | /dev/ttyS3     | 扩展UART (J6)                            |
| UART5  | /dev/ttyS4     | PX4 与 Companion Computer 之间的 UART  |
| USART6 | /dev/ttyS5     | RC (J1004)                               |
| UART7  | /dev/ttyS6     | TELEM1 (J1010)                           |
| UART8  | /dev/ttyS7     | 无                                       |

<!-- 注意：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->

## 支持

请访问[ModalAI论坛](https://forum.modalai.com/category/8/voxl-flight)获取更多信息。