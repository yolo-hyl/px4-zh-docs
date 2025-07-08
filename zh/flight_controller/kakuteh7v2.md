# Holybro Kakute H7 V2

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Holybro Kakute H7 V2](https://holybro.com/collections/autopilot-flight-controllers/products/kakute-h7-v2)飞控器功能丰富，包括集成蓝牙、高清摄像头插孔、双即插即用4合1电调端口、9V VTX 开关凹槽、气压计、OSD、6个UART、128MB闪存用于日志记录（PX4暂不支持）、5V和9V BEC，以及更大的焊盘和更易布局的设计等。

Kakute H7v2 在其前身[Kakute F7](../flight_controller/kakutef7.md)和[Kakute H7](../flight_controller/kakuteh7.md)的最佳功能基础上进行了改进。

该板载有气压计、LED和蜂鸣器焊盘，以及I2C焊盘（SDA & SCL）用于外部GPS/磁力计。

<img src="../../assets/flight_controller/kakuteh7v2/kakuteh7v2_top.png" width="300px" title="KakuteH7V2 Top Image" /> <img src="../../assets/flight_controller/kakuteh7v2/kakuteh7v2_bottom.png" width="300px" title="KakuteH7V2 Bottom Image" />

::: info
此飞控器是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 核心特性

- MCU：STM32H743 32位处理器，运行频率480 MHz
- IMU：BMI270
- 气压计：BMP280
- OSD：AT7456E
- 板载蓝牙芯片：PX4中禁用
- VTX 开关凹槽：PX4中未使用
- 6个UART（1,2,3,4,6,7；UART2用于蓝牙遥测）
- 9个PWM输出（8个电机输出，1个LED）
- 2个JST-SH1.0_8pin端口（用于单个或4合1电调，x8/八旋翼即插即用兼容）
- 1个JST-GH1.5_6pin端口（用于高清系统如Caddx Vista & Air Unit）
- 电池输入电压：2S-8S
- BEC 5V 2A 持续
- BEC 9V 1.5A 持续
- 安装尺寸：30.5 x 30.5mm/Φ4mm孔带Φ3mm垫圈
- 板尺寸：35x35mm
- 重量：8g

## 购买渠道

该板可从以下商店之一购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h7-v2)

:::tip
_Kakute H7v2_ 设计用于与 _Tekko32_ 4合1电调配合使用，它们可以组合购买。
:::

## 连接器和引脚

| 引脚      | 功能                                                          | PX4 默认设置         |
| -------- | ----------------------------------------------------------------- | ------------------- |
| B+       | 电池正极电压（2S-8S）                                  |                     |
| VTX+     | 9V 输出                                                         |                     |
| SDA, SCL | I2C 连接（用于外设）                                  |                     |
| 5V       | 5V 输出（最大2A）                                                |                     |
| 3V3      | 3.3V 输出（最大0.25A）                                           |                     |
| VI       | 来自FPV摄像头的视频输入                                       |                     |
| VO       | 到视频发射器的视频输出                                 |                     |
| CAM      | 到摄像头OSD控制                                             |                     |
| G 或 GND | 地                                                            |                     |
| RSI      | 接收机的模拟RSSI输入（0-3.3V）                          |                     |
| R1, T1   | UART1 RX 和 TX                                                   | TELEM1              |
| R3, T3   | UART3 RX 和 TX                                                   | NuttX调试控制台 |
| R4, T4   | UART4 RX 和 TX                                                   | GPS1                |
| R6, T6   | UART6 RX 和 TX（R6也位于GH插头中）                  | RC端口             |
| R7       | UART7 RX（RX位于插头中，用于4合1电调）    | DShot遥测     |
| LED      | WS2182 可寻址LED信号线（未测试）                   |                     |
| Z-       | 蜂鸣器负极（将蜂鸣器正极连接到5V焊盘） |                     |
| M1 到 M4 | 电机信号输出（位于插头中，用于4合1电调）     |                     |
| M5 到 M8 | 电机信号输出（位于插头中，用于4合1电调）     |                     |
| Boot     | Bootloader按钮                                                 |                     |

<a id="bootloader"></a>

## PX4 Bootloader 更新

该板预装了[Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装PX4固件之前，必须烧录_PX4 Bootloader_。
下载[holybro_kakuteh7v2_bootloader.hex]并按照说明操作。

## 构建固件

使用以下命令构建固件：
```
make holybro_kakuteh7v2_default
```

## 安装固件

安装固件时，请确保使用：
```
PX4 master & PX4 v1.14 or newer
```

## 配置

参数表中，例如：
- **SYS_HAS_MAG**：应禁用此参数...