# MindPX 硬件

:::warning  
PX4 不生产任何自动驾驶仪（含本产品）。  
如需硬件支持或合规性咨询，请联系[制造商](http://mindpx.net)。  
:::

AirMind<sup>&reg;</sup> [MindPX](http://mindpx.net) 系列是源自 Pixhawk<sup>&reg;</sup> 的新一代自动驾驶仪系统。

![MindPX 控制器](../../assets/hardware/hardware-mindpx.png)  

::: info  
这些飞控系统已获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。  
:::

## 快速概述  

::: info  
主要硬件文档请见[此处](http://mindpx.net/assets/accessories/Specification9.18_3_pdf.pdf)。  
:::

MindPX 是源自 Pixhawk<sup>&reg;</sup> 的新一代自动驾驶仪系统，经过原理图与结构的全面修订，并新增功能以提升无人机体的智能化程度与易用性。  

MindPX 将 PWM 输出通道总数提升至 16 个（8 个主输出 + 8 个辅输出），这意味着可支持更复杂的 VTOL 配置和更精细的控制。对于基于 FMU-V4 的飞控系统尤其重要，因为 MindPX 在单个 FMU 中实现了主输出与辅输出。  

![](../../assets/hardware/hardware-mindpx-specs.png)  

- 主控芯片：STM32F427  

  - CPU：32 位，168 MHz ARM Cortex<sup>&reg;</sup> M4 带 FPU  
  - RAM：256 KB SRAM  
  - 2MB Flash  
  - ST Micro LSM303D 14 位加速度计/磁力计  
  - MEAS MS5611 气压计  
  - InvenSense<sup>&reg;</sup> MPU6500 集成六轴传感器  

- 主要特性：  
  - CNC 加工铝合金外壳，轻便且坚固  
  - 内置隔离式 IMU 冗余  
  - 总计 16 个 PWM 输出通道（8 个主 + 8 个辅）  
  - 1 个额外 I2C 接口用于流传感器连接  
  - 1 个额外 USB 接口用于连接伴飞电脑（内置 UART-USB 转换器）  
  - 暴露的调试端口用于开发  

## 快速入门  

### 安装  

![MindPX 安装](../../assets/hardware/hardware-mindpx-mounting.png)  

### 接线  

![MindPX 接线 1](../../assets/hardware/hardware-mindpx-wiring1.png)  

![MindPX 接线 2](../../assets/hardware/hardware-mindpx-wiring2.png)  

### 引脚  

![MindPX 引脚图](../../assets/hardware/hardware-mindpx-pin.png)  

| 序号 |        描述         | 序号 |       描述        |
| :--: | :-----------------: | :--: | :---------------: |
|  1   |         电源         |  9   | I2C2 (MindFLow)  |
|  2   | 调试（刷新引导程序） |  10  | USB2 (串口 2 转 USB) |
|  3   | USB1（刷新固件）    |  11  |     UART4,5      |
|  4   |        复位         |  12  | UART1（遥测）    |
|  5   |     UART3（GPS）    |  13  |       CAN        |
|  6   | I2C1（外部罗盘）    |  14  |       ADC        |
|  7   |     TF 卡插槽       |  15  |    三色灯        |
|  8   | NRF/SPI（遥控器）   |  16  |      Looper      |

### 无线电接收器  

MindPX 支持多种无线电接收器（自 V2.6 起），包括：PPM/SBUS/DSM/DSM2/DSMX。  
MindPX 还支持 FrSky<sup>&reg;</sup> 双向遥测 D 和 S.Port。  

详细的引脚图请参阅[用户指南](http://mindpx.net/assets/accessories/UserGuide9.18_2_pdf.pdf)。  

### 编译固件  

:::tip  
大多数用户无需编译此固件！  
当连接相应硬件时，_QGroundControl_ 会自动安装预编译版本。  
:::

要为该目标[编译 PX4](../dev_setup/building_px4.md)：

```
make airmind_mindpx-v2_default
```

### 伴飞电脑连接  

MindPX 板载 USB-TO-UART 桥接芯片。  
使用 Micro-USB 至 USB Type-A 线缆连接。将 Micro-USB 端连接到 MindPX 的 'OBC' 接口，USB Type-A 端连接到伴飞电脑。  

最大波特率与 PX4 家族一致，最高可达 921600。  

## 用户指南  

::: info  
用户指南请见[此处](http://mindpx.net/assets/accessories/UserGuide9.18_2_pdf.pdf)。  
:::

## 购买渠道  

MindRacer 可通过 [AirMind Store](http://drupal.xitronet.com/?q=catalog) 购买。  
您也可以在 Amazon<sup>&reg;</sup> 或 eBay<sup>&reg;</sup> 找到 MindRacer。  

## 串口映射  

| UART   | 设备      | 端口         |
| ------ | --------- | ------------ |
| USART1 | /dev/ttyS0 | RC           |
| USART2 | /dev/ttyS1 | TELEM1       |
| USART3 | /dev/ttyS2 | TELEM2       |
| UART4  | /dev/ttyS3 | GPS1         |
| USART6 | /dev/ttyS4 | ?            |
| UART7  | /dev/ttyS5 | 调试控制台   |
| UART8  | /dev/ttyS6 | ?            |

<!-- 注意：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口信息 -->

## 支持  

请访问 http://www.mindpx.org 获取更多信息。  
或发送邮件至 [support@mindpx.net](mailto:support@mindpx.net) 进行任何咨询或寻求帮助。