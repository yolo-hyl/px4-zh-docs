# ModalAI VOXL Flight

<Badge type="tip" text="PX4 v1.11" />

:::warning
PX4 不生产此（或任何）自动驾驶仪。  
有关硬件支持或合规性问题，请联系[制造商](https://forum.modalai.com/)。
:::

ModalAI [VOXL Flight](https://modalai.com/voxl-flight) ([技术规格书](https://docs.modalai.com/voxl-flight-datasheet)) 是首批将Snapdragon的性能与复杂性与STM32F7上PX4的灵活性及易用性结合的计算平台之一。  
作为美国制造的产品，VOXL Flight通过单块PCB实现避障功能和无GPS（室内）导航，这些功能与PX4飞控融合。

![VOXL-Flight](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-dk.jpg)

::: info
此飞控是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 规格

### 系统

| 功能 | 详情 |
| :------ | :------ |
| 重量 | 26 克 |

### 伴随计算机

| 功能 | 详情 |
| :-------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 基础操作系统 | Linux Yocto Jethro 搭载 3.18 内核。可通过 Docker 在 VOXL 上运行其他 Linux 操作系统，详情 [here](https://docs.modalai.com/docker-on-voxl/) |
| 计算能力 | Qualcomm Snapdragon 821 搭载 4GB LPDDR4 1866MHz，Snapdragon 821 [Datasheet](https://developer.qualcomm.com/download/sd820e/qualcomm-snapdragon-820e-processor-apq8096sge-device-specification.pdf)，[Docs](https://developer.qualcomm.com/hardware/apq-8096sg/tools) |
| CPU | 四核 CPU 最高 2.15GHz |
| GPU | Adreno 530 GPU 624MHz |
| 计算 DSP | Hexagon 计算 DSP (cDSP) 825MHz |
| 传感器 DSP | Hexagon 传感器 DSP (sDSP) 700MHz |
| 视频 | 4k30 视频捕捉 h.264/5 带 720p FPV |
| 摄像头接口 | 支持 MIPI-CSI2、USB UVC、HDMI |
| Wi-Fi | 预认证 Wi-Fi 模块 [QCNFA324 FCC ID:PPD-QCNFA324](https://fccid.io/PPD-QCNFA324)，QCA6174A 调制解调器，802.11ac 2x2 双频，蓝牙 4.2 (双模式) |
| 4G LTE | [可选扩展模块](https://www.modalai.com/collections/voxl-add-ons/products/voxl-lte) |
| Microhard pDDL | [可选扩展模块](https://www.modalai.com/collections/voxl-add-ons/products/voxl-microhard-modem-usb-hub) |
| GNSS | WGR7640 10Hz |
| I/O | 1x USB3.0 OTG (ADB 端口)，1x USB2.0 (扩展端口)，2x UART，3x I2C，可配置额外 GPIO 和 SPI |
| 存储 | 32GB (UFS 2.0)，Micro SD 卡 |
| 软件 | Docker，OpenCV 2.4.11、3.4.6、4.2，ROS Indigo，Qualcomm 机器视觉 SDK，详见 [GitLab](https://gitlab.com/voxl-public) 获取大量开源示例！ |
| IMUs | ICM-42688 (SPI10)，ICM-20948 (SPI1) |
| 气压计 | BMP280 |

### 飞行控制器

| 功能 | 详情 |
| :--------------- | :--------------------------------------------------------------- |
| MCU | 216MHz，32 位 ARM M7 [STM32F765II][stm32f765ii] |
| 内存 | 256Kb FRAM |
|  | 2Mbit Flash |
|  | 512Kbit SRAM |
| 固件 | [PX4][px4] |
| IMUs | [ICM-20602][icm-20602] (SPI1) |
|  | ICM-42688 (SPI2) |
|  | [BMI088][bmi088] (SPI6) |
| 气压计 | [BMP388][bmp388] (I2C4) |
| 安全元件 | [A71CH][a71ch] (I2C4) |
| microSD 卡 | [支持卡信息](../dev_log/logging.md#sd-cards) |
| 输入 | GPS/磁力计 |
|  | Spektrum |
|  | 通信遥测 |
|  | CAN 总线 |
|  | PPM |
| 输出 | 6 个 LED (2xRGB) |
|  | 8 个 PWM 通道 |
| 附加接口 | 3 个串口 |
|  | I2C |
|  | GPIO |

<!-- 上表参考链接 (改进布局) -->

[stm32f765ii]: https://www.st.com/en/microcontrollers-microprocessors/stm32f765ii.html
[px4]: https://github.com/PX4/PX4-Autopilot/tree/main/boards/modalai/fc-v1
[icm-20602]: https://www.invensense.com/products/motion-tracking/6-axis/icm-20602/
[bmi088]: https://www.bosch-sensortec.com/bst/products/all_products/bmi088_1
[bmp388]: https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp388/
[a71ch]: https://www.nxp.com/products/security-and-authentication/authentication/plug-and-trust-the-fast-easy-way-to-deploy-secure-iot-connections:A71CH

::: info
更详细的硬件文档请见 [here](https://docs.modalai.com/voxl-flight-datasheet/)。
:::

## 尺寸

![FlightCoreV1Dimensions](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-dimensions.jpg)

[3D STEP 文件](https://storage.googleapis.com/modalai_public/modal_drawings/M0019_VOXL-Flight.zip)

## PX4 固件兼容性

_VOXL Flight_ 完全兼容 PX4 官方固件（从 PX4 v1.11 开始）。

ModalAI 为 PX4 v1.11 维护了一个[分支版 PX4](https://github.com/modalai/px4-firmware/tree/modalai-1.11)。该版本包含 UART ESC 支持以及 VIO 和 VOA 的改进功能，这些改进计划提交至上游主版本。

更多关于固件的信息请[访问此处](https://docs.modalai.com/flight-core-firmware/)。

## QGroundControl Support

此板在 QGroundControl 4.0 及更高版本中受到支持。

## 可用性

- [VOXL Flight Complete Kit](https://modalai.com/voxl-flight)
- [VOXL Flight Board](https://www.modalai.com/products/voxl-flight?variant=31707275362355) (only)
- [VOXL Flight 集成了避障摄像头的飞行平台（VOXL Flight Deck）](https://modalai.com/flight-deck) ([Datasheet](https://docs.modalai.com/voxl-flight-deck-platform-datasheet/))
- [即飞型 VOXL m500 开发无人机中的 VOXL Flight](https://www.modalai.com/collections/development-drones/products/voxl-m500) ([Datasheet](https://docs.modalai.com/voxl-m500-reference-drone-datasheet/))

## 快速入门  

供应商提供的快速入门指南位于 [此处](https://docs.modalai.com/voxl-flight-quickstart/)。  

### voxl-vision-px4  

VOXL Flight 在硬件的伴飞计算机部分运行 [voxl-vision-px4](https://gitlab.com/voxl-public/modal-pipe-architecture/voxl-vision-px4)，作为一种 MAVLink 代理。  
详情请参考源代码 [此处](https://gitlab.com/voxl-public/modal-pipe-architecture/voxl-vision-px4)。  

### 连接器  

关于引脚分配的详细信息请见 [此处](https://docs.modalai.com/voxl-flight-datasheet-connectors/)。  

#### 顶部  

![VOXLFlightTop](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-top.jpg)  

_注意：1000系列连接器可通过 STM32/PX4 访问_  

| 连接器 | 简介                                  | 使用方                           |  
| ------ | ------------------------------------- | -------------------------------- |  
| J2     | 高清 4K 图像传感器 (CSI0)            | Snapdragon - Linux               |  
| J3     | 立体图像传感器 (CSI1)                | Snapdragon - Linux               |  
| J6     | 散热风扇连接器                        | Snapdragon - Linux               |  
| J7     | BLSP6 (GPIO) 和 BLSP9 (UART)         | Snapdragon - Linux               |  
| J13    | 扩展 B2B                              | Snapdragon - Linux               |  
| J14    | 集成 GNSS 天线连接                    | Snapdragon - Linux               |  
| J1001  | 编程和调试/UART3                      | STM32 - PX4                      |  
| J1002  | 电调 UART, UART2/TELEM3              | STM32 - PX4                      |  
| J1003  | PPM 信号输入                          | STM32 - PX4                      |  
| J1004  | 信号输入，Spektrum/SBus/UART6         | STM32 - PX4                      |  
| J1006  | USB 2.0 接口 (PX4/QGroundControl)     | STM32 - PX4                      |  
| J1007  | 8 通道 PWM/DShot 输出                 | STM32 - PX4                      |  
| J1008  | CAN 总线                              | STM32 - PX4                      |  
| J1009  | I2C3, UART4                           | STM32 - PX4                      |  
| J1010  | 通信链路 (TELEM1)                     | STM32 - PX4                      |  
| J1011  | I2C2, 安全按钮输入                    | STM32 - PX4                      |  
| J1012  | 外部 GPS & 磁力计，UART1, I2C1        | STM32 - PX4                      |  
| J1013  | 电源输入，I2C3                        | STM32 - PX4 (为整个系统供电)     |  

#### 底部  

![VOXLFlightBottom](../../assets/flight_controller/modalai/voxl_flight/voxl-flight-bottom.jpg)  

_注意：1000系列连接器可通过 STM32/PX4 访问_  

| 连接器       | 简介                                 | 使用方                     |  
| ------------ | ------------------------------------ | -------------------------- |  
| J4           | 跟踪/光流图像传感器 (CSI2)          | Snapdragon - Linux         |  
| J8           | USB 3.0 OTG                          | Snapdragon - Linux, **adb** |  
| J10          | BLSP7 UART 和 I2C 外部接口          | Snapdragon - Linux         |  
| J11          | BLSP12 UART 和 I2C 外部接口         | Snapdragon - Linux         |  
| VOXL microSD |                                     | Snapdragon - Linux         |  
| PX4 microSD  | 最大 32GB                            | STM32 - PX4                |  
| Wi-Fi 天线   | 已包含                               | Snapdragon - Linux         |  

### 用户指南  

完整的用户指南请见 [此处](https://docs.modalai.com/voxl-flight-quickstart)。  

### 如何构建  

要为该目标[构建 PX4](../dev_setup/building_px4.md)：  

```
make modalai_fc-v1
```

## 串口映射

_注意: 所示映射仅适用于PX4控制的接口_

| UART   | 设备             | 端口                                      |
| ------ | ---------------- | ----------------------------------------- |
| USART1 | /dev/ttyS0       | GPS1 (J1012)                              |
| USART2 | /dev/ttyS1       | TELEM3 (J1002)                            |
| USART3 | /dev/ttyS2       | 调试控制台 (J1001)                        |
| UART4  | /dev/ttyS3       | 扩展UART (J6)                             |
| UART5  | /dev/ttyS4       | PX4与伴侣计算机之间的UART               |
| USART6 | /dev/ttyS5       | 遥控接收机 (J1004)                        |
| UART7  | /dev/ttyS6       | TELEM1 (J1010)                            |
| UART8  | /dev/ttyS7       | 不适用                                    |

<!-- 注意: 通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口信息 -->## 支持

请访问 [ModalAI 论坛](https://forum.modalai.com/category/8/voxl-flight) 以获取更多信息。