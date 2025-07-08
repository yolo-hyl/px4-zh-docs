# Holybro Kakute H7 mini

<Badge type="tip" text="PX4 v1.13" />

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Holybro Kakute H7 mini](https://holybro.com/collections/autopilot-flight-controllers/products/kakute-h7-mini) 飞行控制器适用于轻量级机架组装（如竞速飞行器等）。

该飞行控制器功能丰富，包括高清摄像头插孔、双即插即用4合1电调端口、VTX 开/关拨杆开关（电池电压）、气压计、OSD、6个UART、128MB闪存（用于日志记录，PX4暂不支持）、5V BEC、更大的焊盘和更简单的布局等。

Kakute H7 mini 在其前代产品 [Kakute F7](../flight_controller/kakutef7.md) 和 [Kakute H7](../flight_controller/kakuteh7.md) 的最佳特性基础上进行了改进。该板载有气压计、LED & 蜂鸣器焊盘和 I2C 焊盘（SDA & SCL）用于外部GPS/磁力计。

<img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_top.jpg" width="300px" title="KakuteH7Mini Top Image" /> <img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_bottom.jpg" width="300px" title="KakuteH7Mini Bottom Image" />

::: info
该飞行控制器受[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

::: info
PX4 运行于 H7 mini v1.3 及更高版本。
:::

## 核心特性

- MCU：STM32H743 32位处理器，运行频率480 MHz
- IMU：BMI270
- 气压计：BMP280
- OSD：AT7456E
- 6个UART（1,2,3,4,6,7）
- VTX 开/关拨杆开关：PX4中未使用
- 9个PWM输出（8个电机输出，1个LED）
- 2个JST-SH1.0_8pin端口（用于单电调或4合1电调，x8/八旋翼即插即用兼容）
- 1个JST-GH1.5_6pin端口（用于高清系统，如Caddx Vista & Air Unit）
- 电池输入电压：2S-6S
- BEC 5V 2A Cont.
- 安装：20 x 20mm/Φ3.6mm孔，带M3 & M2衬套
- 尺寸：30x31x6mm
- 重量：5.5g

## 购买途径

该板可以从以下店铺购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h7-mini)

## 连接器与引脚

<img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_pinout.jpg" width="300px" title="KakuteH7Mini Pinout Image" />

| 引脚      | 功能                                                          | PX4 默认         |
| -------- | ----------------------------------------------------------------- | ------------------- |
| B+       | 电池正电压（2S-6S）                                  |                     |
| VTX+     | 电池正电压（2S-6S）                                  |                     |
| SDA, SCL | I2C连接（用于外设）                                  |                     |
| 5V       | 5V输出（最大2A）                                                |                     |
| 3V3      | 3.3V输出（最大0.25A）                                           |                     |
| VI       | 来自FPV摄像头的视频输入                                       |                     |
| VO       | 视频输出到视频发射器                                 |                     |
| CAM      | 连接相机OSD控制                                             |                     |
| G 或 GND | 地线                                                            |                     |
| RSI      | 来自接收机的模拟RSSI输入（0-3.3V）                          |                     |
| R1, T1   | UART1 RX和TX                                                   | TELEM1              |
| R2, T2   | UART2 RX和TX                                                   | TELEM2              |
| R3, T3   | UART3 RX和TX                                                   | NuttX调试控制台 |
| R4, T4   | UART4 RX和TX                                                   | GPS1                |
| R6, T6   | UART6 RX和TX（R6也位于GH插头中）                  | RC端口             |
| R7       | UART7 RX（RX位于插头中，用于4合1电调）    | DShot遥测     |
| LED      | WS2182可寻址LED信号线（未测试）                             |                     |
| GND      | 地线（如板载标记）                                           |                     |
| VDD_3V3  | 3V3电源（如板载标记）                                       |                     |

<a name="bootloader-update"></a>
## PX4启动加载程序更新

该板使用[PX4 Bootloader](https://github.com/PX4/Bootloader)进行固件更新。要更新启动加载程序，请按照以下步骤操作：

1. 下载最新启动加载程序：[PX4 Bootloader](https://github.com/PX4/Bootloader)
2. 使用[Bootloader操作指南](https://github.com/PX4/Bootloader#bootloader-operations)中的说明进行更新

## 构建固件

要构建PX4固件，请运行以下命令：

```bash
make px4_fmu-v5_default
```

## 安装PX4固件

安装固件时，请确保使用最新版本的[PX4 Firmware](https://github.com/PX4/PX4-Autopilot)。安装步骤如下：

1. 下载最新固件：[PX4 Firmware](https://github.com/PX4/PX4-Autopilot)
2. 使用[安装指南](https://docs.px4.io/main/en/dev_setup/building_px4.html)中的说明进行安装

## 配置

该板支持以下配置：

- **飞控模式**：支持所有标准飞控模式
- **传感器校准**：使用QGroundControl进行校准
- **参数调整**：通过QGroundControl或MAVLink进行调整

## 串口映射

| 串口   | 功能               | 默认波特率 |
|--------|--------------------|------------|
| TELEM1 | 串口1（默认遥测）   | 57600      |
| TELEM2 | 串口2（备用遥测）   | 57600      |
| GPS    | 串口3（GPS）        | 921600     |
| SERIAL4 | 串口4（备用）      | 115200     |
| SERIAL5 | 串口5（备用）      | 115200     |

## 调试端口

该板提供以下调试端口：

- **SWD接口**：
  - SWDIO：Test Point 3（CPU第76引脚）
  - SWCLK：Test Point 2（CPU第74引脚）
  - GND：板载标记
  - VDD_3V3：板载标记

- **UART调试**：
  - 使用TELEM1或TELEM2进行调试
  - 默认波特率：57600

确保在调试时连接正确的电源和地线。