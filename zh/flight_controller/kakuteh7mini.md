

# Holybro Kakute H7 mini

<Badge type="tip" text="PX4 v1.13" />

:::warning
PX4 不生产此（或任何）自动驾驶仪。  
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Holybro Kakute H7 mini](https://holybro.com/collections/autopilot-flight-controllers/products/kakute-h7-mini) 飞行控制器适用于轻量化框架的搭建（如竞速无人机等）。

该飞控板功能丰富，包括高清摄像头插孔、双即插即用4合1电调端口、VTX开关（电池电压）、气压计、OSD、6个UARTs、128MB闪存用于日志记录（目前PX4不支持）、5V BEC以及更大尺寸的焊盘和更易布局设计等。

Kakute H7 mini 继承了前代产品 [Kakute F7](../flight_controller/kakutef7.md) 和 [Kakute H7](../flight_controller/kakuteh7.md) 的最佳特性。该板载有气压计、LED与蜂鸣器焊盘以及I2C接口（SDA & SCL）用于外接GPS/磁力计。

<img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_top.jpg" width="300px" title="KakuteH7Mini Top Image" /> <img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_bottom.jpg" width="300px" title="KakuteH7Mini Bottom Image" />

::: info
此飞控板为[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

::: info
PX4 支持 H7 mini v1.3 及后续版本。
:::

## 核心功能

- 微控制器：STM32H743 32位处理器（480 MHz主频）
- 惯性测量单元：BMI270
- 气压计：BMP280
- 画中画显示器：AT7456E
- 6个UART接口（1,2,3,4,6,7）
- 视频发射器开关：不兼容PX4
- 9个PWM输出（8个电机输出，1个LED）
- 2个JST-SH1.0_8pin接口（支持单电调/四合一电调，x8/八轴飞行器即插即用兼容）
- 1个JST-GH1.5_6pin接口（支持Caddx Vista & Air Unit等高清系统）
- 电池输入电压：2S-6S
- 备用电源5V 2A持续
- 安装方式：20 x 20mm/Φ3.6mm孔位（M3 & M2垫圈兼容）
- 尺寸：30x31x6mm
- 重量：5.5g

## 购买地点

该板可以从以下商店之一购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h7-mini)

## 连接器和引脚

<img src="../../assets/flight_controller/kakuteh7mini/kakuteh7mini_pinout.jpg" width="300px" title="KakuteH7Mini Pinout Image" />

| 引脚      | 功能                                                          | PX4默认设置         |
| -------- | ----------------------------------------------------------------- | ------------------- |
| B+       | 电池正极电压（2S-6S）                                  |                     |
| VTX+     | 电池正极电压（2S-6S）                                  |                     |
| SDA, SCL | I2C连接（用于外设）                                  |                     |
| 5V       | 5V输出（最大2A）                                                |                     |
| 3V3      | 3.3V输出（最大0.25A）                                           |                     |
| VI       | 来自FPV摄像头的视频输入                                       |                     |
| VO       | 到视频发射器的视频输出                                 |                     |
| CAM      | 至摄像头OSD控制                                             |                     |
| G 或 GND | 接地                                                            |                     |
| RSI      | 来自接收机的模拟RSSI（0-3.3V）输入                          |                     |
| R1, T1   | UART1 接收和发送                                                   | TELEM1              |
| R2, T2   | UART2 接收和发送                                                   | TELEM2              |
| R3, T3   | UART3 接收和发送                                                   | NuttX调试控制台 |
| R4, T4   | UART4 接收和发送                                                   | GPS1                |
| R6, T6   | UART6 接收和发送（R6也位于GH插头中）                  | 接收机端口             |
| R7       | UART7 接收（RX位于插头中，用于4合一电调）    | DShot遥测     |
| LED      | WS2182可寻址LED信号线（未测试）                   |                     |
| Z-       | 蜂鸣器负极引脚（蜂鸣器正极接5V焊盘） |                     |
| M1到M4   | 电机信号输出（位于插头中，用于4合一电调）     |                     |
| M5到M8   | 电机信号输出（位于插头中，用于4合一电调）     |                     |
| Boot     | Bootloader按钮                                                 |                     |

<a id="bootloader"></a>

## PX4 引导程序更新

该开发板预装了 [Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装PX4固件之前，必须烧录 _PX4 引导程序_。
下载 [holybro_kakuteh7mini_bootloader.hex](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/kakuteh7mini/holybro_kakuteh7mini_bootloader.hex) 引导程序二进制文件，并阅读 [this page](../advanced_config/bootloader_update_from_betaflight.md) 中的烧录说明。

## 构建固件

要[构建 PX4](../dev_setup/building_px4.md)以针对此目标：

```
make holybro_kakuteh7mini_default
```

## 安装 PX4 固件

::: info
如果通过 QGroundcontrol 加载预编译的固件，必须使用 QGC Daily 或 4.1.7 及以上版本的 QGC。
在此版本之前需要手动编译并安装固件。
:::

固件可以通过以下常规方式进行手动安装：

- 编译并上传源代码：

  ```
  make holybro_kakuteh7mini_default upload
  ```

- [加载固件](../config/firmware.md) 使用 _QGroundControl_。
  你可以使用预编译的固件或自己的自定义固件。

::: info
KakuteH7mini 支持 PX4 main 和 v1.14 或更高版本。
:::

## PX4配置

除了[basic configuration](../config/index.md)外，以下参数也很重要：

| 参数                                                            | 设置                                                                                                                 |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG) | 应该禁用，因为该板没有内部磁力计。如果连接了外部磁力计，可以启用它。 |

## 串口映射

| 串口   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| USART1 | /dev/ttyS0 | TELEM1                |
| UART2  | /dev/ttyS1 | TELEM2                |
| USART3 | /dev/ttyS2 | 调试控制台            |
| UART4  | /dev/ttyS3 | GPS1                  |
| USART6 | /dev/ttyS4 | 遥控SBUS              |
| UART7  | /dev/ttyS5 | 电调遥测（DShot）     |

## 调试端口

### 系统控制台

UART3 RX 和 TX 被配置为用作 [系统控制台](../debug/system_console.md)。

### SWD

[SWD接口](../debug/swd_debug.md) (JTAG) 引脚为：

- `SWCLK`: 测试点2（CPU上的72号引脚）
- `SWDIO`: 测试点3（CPU上的76号引脚）
- `GND`: 如板上所标记
- `VDD_3V3`: 如板上所标记