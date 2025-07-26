

# Holybro Kakute H7

<Badge type="tip" text="PX4 v1.13" />

:::warning
PX4 不生产此(或任何)飞控。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Holybro Kakute H7](https://holybro.com/products/kakute-h7) 集成众多特性，包括双即插即用四合一电调接口、高清摄像头插槽、气压计、OSD、6个UART、完整黑匣子MicroSD卡插槽、5V和9V BEC、简易焊接布局等。

Kakute H7 继承了前代产品[Kakute F7](../flight_controller/kakutef7.md)的最佳特性，并进一步优化了硬件组件和布局。双即插即用四合一电调连接器简化了x8和八旋翼配置的支持，保持组装简洁有序。

该板载有气压计、LED与蜂鸣器插槽，以及I2C插槽(SDA & SCL)用于连接外部GPS/磁力计。

![Kakute h7](../../assets/flight_controller/kakuteh7/kakuteh7.png)

::: info
此飞控已获得[厂商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 核心功能

- MCU: STM32H743 32位处理器，运行频率480 MHz
- IMU: MPU6000
- 气压计: BMP280
- OSD: AT7456E
- 板载蓝牙芯片: PX4中已禁用
- 2x JST-SH1.0_8针接口 (支持单电调/四合一电调，x8/八旋翼即插即用兼容)
- 1x JST-GH1.25_6针接口 (适用于Caddx Vista & Air Unit等高清系统)
- 电池输入电压: 2S - 8S
- BEC 5V 2A 持续输出
- BEC 9V 1.5A 持续输出
- 安装尺寸: 30.5 x 30.5mm/Φ4mm孔带Φ3mm密封圈
- 板体尺寸: 35x35mm
- 重量: 8g

## 购买渠道

该板可以从以下商店之一购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h7)

:::tip
_Kakute H7_ 设计用于与 _Tekko32_ 4-in-1 电调配合使用，两者可组合购买。
:::

## 连接器和引脚

这是 _Kakute H7_ 的丝印图，显示了电路板的顶部：

<img src="../../assets/flight_controller/kakuteh7/kakuteh7_silk.png" width="380px" title="Kakute h7" />

| 引脚      | 功能                                                          | PX4 默认设置         |
| -------- | ----------------------------------------------------------------- | ------------------- |
| B+       | 电池正极电压 (2S-8S)                                  |                     |
| SDA, SCL | I2C 接口 (用于外设)                                  |                     |
| 5V       | 5V 输出 (最大2A)                                                |                     |
| 3V3      | 3.3V 输出 (最大0.25A)                                           |                     |
| VI       | 来自FPV摄像头的视频输入                                       |                     |
| VO       | 视频输出至视频发射器                                 |                     |
| CAM      | 连接摄像头OSD控制                                             |                     |
| G 或 GND | 接地                                                            |                     |
| RSI      | 来自接收机的模拟RSSI输入 (0-3.3V)                          |                     |
| R1, T1   | UART1 RX 和 TX                                                   | TELEM1              |
| R3, T3   | UART3 RX 和 TX                                                   | NuttX 调试控制台 |
| R4, T4   | UART4 RX 和 TX                                                   | GPS1                |
| R6, T6   | UART6 RX 和 TX (R6 也位于GH插头内)                  | RC端口             |
| R7       | UART7 RX (RX 位于插头内，用于4合一电调)    | DShot 通信     |
| LED      | WS2182 可寻址LED信号线 (未测试)                   |                     |
| Z-       | 蜂鸣器负极 (将蜂鸣器正极连接到5V焊盘) |                     |
| M1 到 M4 | 电机信号输出 (位于插头内，用于4合一电调)     |                     |
| M5 到 M8 | 电机信号输出 (位于插头内，用于4合一电调)     |                     |
| Boot     | 引导加载程序按钮                                                 |                     |

## PX4 Bootloader更新 {#bootloader}

该开发板预装了[Betaflight](https://github.com/betaflight/betaflight/wiki)。  
在安装PX4固件之前，必须烧录_PX4 Bootloader_。  
下载[kakuteh7_bl.hex](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/kakuteh7/holybro_kakuteh7_bootloader.hex) Bootloader二进制文件，并阅读[此页面](../advanced_config/bootloader_update_from_betaflight.md)以获取烧录说明。

## 构建固件

要 [构建 PX4](../dev_setup/building_px4.md) 为此目标：

```sh
make holybro_kakuteh7_default
```

## 安装 PX4 固件

固件可以通过以下常规方式安装：

- 编译源码并上传

  ```sh
  make holybro_kakuteh7_default upload
  ```

- [加载固件](../config/firmware.md) 使用 _QGroundControl_。
  可以使用预构建固件或自定义固件。

::: info
如果通过 QGroundControl 加载预构建固件，必须使用 QGC Daily 或 4.1.7 之后的 QGC 版本。
:::

## PX4配置

除了[基本配置](../config/index.md)外，以下参数也非常重要：

| 参数                                                            | 设置                                                                                                                 |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG) | 由于开发板没有内置磁力计，此参数应禁用。如果连接了外部磁力计，可以启用此参数。 |

## 串口映射

| UART   | 设备     | 端口                  |
| ------ | -------- | --------------------- |
| USART1 | /dev/ttyS0 | TELEM1                |
| USART2 | /dev/ttyS1 | TELEM2                |
| USART3 | /dev/ttyS2 | 调试控制台            |
| UART4  | /dev/ttyS3 | GPS1                  |
| USART6 | /dev/ttyS4 | 遥控器SBUS            |
| UART7  | /dev/ttyS5 | 电调遥测（DShot）     |

### 使用 TELEM2 (USART2)

`TELEM2` 端口 (USART2) 没有暴露的焊盘，因为该端口设计用于蓝牙遥测（这与 PX4 不兼容）。

你可以通过移除两个标记为 X 的电阻来暴露焊盘并使用该端口。
无需其他配置。

<img src="../../assets/flight_controller/kakuteh7/kakuteh7_uart2.png" width="380px" title="Kakute h7" />

## 调试端口

### 系统控制台

UART3的RX和TX被配置为用作[系统控制台](../debug/system_console.md)。

### SWD

[SWD接口](../debug/swd_debug.md) (JTAG) 引脚如下：

- `SWCLK`: 测试点2（CPU上的第72针脚）
- `SWDIO`: 测试点3（CPU上的第76针脚）
- `GND`: 如板上所标示
- `VDD_3V3`: 如板上所标示

如下图所示。

![SWD Pins on Kakute H7 - CLK SWO](../../assets/flight_controller/kakuteh7/kakuteh7_debug_swd_port.jpg)