

# Holybro Kakute H7 V2

:::warning
PX4 并不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Holybro Kakute H7 V2](https://holybro.com/collections/autopilot-flight-controllers/products/kakute-h7-v2) 飞行控制器功能丰富，包含集成蓝牙、高清摄像头插孔、双即插即用4合1电调端口、9V VTX 开关调制解调器、气压计、OSD、6个UART、128MB闪存用于日志记录（PX4暂不支持）、5V和9V BEC，以及更大的焊盘和更易布局设计等更多功能。

Kakute H7v2 在其前身 [Kakute F7](../flight_controller/kakutef7.md) 和 [Kakute H7](../flight_controller/kakuteh7.md) 的最佳特性基础上进行了升级。

该电路板还配备了板载气压计、LED与蜂鸣器焊盘，以及用于外部GPS/磁力计的I2C焊盘（SDA与SCL）。

<img src="../../assets/flight_controller/kakuteh7v2/kakuteh7v2_top.png" width="300px" title="KakuteH7V2 Top Image" /> <img src="../../assets/flight_controller/kakuteh7v2/kakuteh7v2_bottom.png" width="300px" title="KakuteH7V2 Bottom Image" />

::: info
此飞行控制器为[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)型号。
:::

## 核心功能

- MCU：STM32H743 32位处理器，运行频率480 MHz
- IMU：BMI270
- 气压计：BMP280
- OSD：AT7456E
- 板载蓝牙芯片：在PX4中禁用
- VTX 开/关训练开关：在PX4中未使用
- 6个UART接口（1,2,3,4,6,7；UART2用于蓝牙遥测）
- 9个PWM输出（8个电机输出，1个LED）
- 2个JST-SH1.0_8pin接口（支持单电调或4合1电调，x8/八旋翼即插即用兼容）
- 1个JST-GH1.5_6pin接口（用于高清系统如Caddx Vista & Air Unit）
- 电池输入电压：2S-8S
- BEC 5V 2A 持续输出
- BEC 9V 1.5A 持续输出
- 安装尺寸：30.5 x 30.5mm/Φ4mm孔带Φ3mm垫圈
- 板体尺寸：35x35mm
- 重量：8g

## 购买渠道

该板可以从以下商店之一购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h7-v2)

:::tip
_Kakute H7v2_ 专为与 _Tekko32_ 四合一电调配合使用而设计，它们可以组合购买。
:::

## 连接器和引脚

| 引脚      | 功能                                                          | PX4默认设置         |
| -------- | ----------------------------------------------------------------- | ------------------- |
| B+       | 电池正极电压（2S-8S）                                  |                     |
| VTX+     | 9V输出                                                         |                     |
| SDA, SCL | I2C连接（用于外设）                                  |                     |
| 5V       | 5V输出（最大2A）                                                |                     |
| 3V3      | 3.3V输出（最大0.25A）                                           |                     |
| VI       | 来自FPV摄像头的视频输入                                       |                     |
| VO       | 传输至视频发射器的视频输出                                 |                     |
| CAM      | 至摄像头OSD控制                                             |                     |
| G 或 GND | 接地                                                            |                     |
| RSI      | 来自接收机的模拟RSSI输入（0-3.3V）                          |                     |
| R1, T1   | UART1接收和发送                                                   | TELEM1              |
| R3, T3   | UART3接收和发送                                                   | NuttX调试控制台 |
| R4, T4   | UART4接收和发送                                                   | GPS1                |
| R6, T6   | UART6接收和发送（R6也位于GH插头中）                  | RC端口             |
| R7       | UART7接收（RX位于插头中，用于4合1电调）    | DShot遥测     |
| LED      | WS2182可寻址LED信号线（未测试）                   |                     |
| Z-       | 压电蜂鸣器负极（将蜂鸣器正极连接至5V焊盘） |                     |
| M1至M4 | 电机信号输出（位于插头中，用于4合1电调）     |                     |
| M5至M8 | 电机信号输出（位于插头中，用于4合1电调）     |                     |
| Boot     | 引导加载程序按钮                                                 |                     |

<a id="bootloader"></a>

## PX4 引导程序更新

该板载预装了 [Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装PX4固件之前，必须烧录 _PX4引导程序_。
下载 [holybro_kakuteh7v2_bootloader.hex](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/kakuteh7v2/holybro_kakuteh7v2_bootloader.hex) 引导程序二进制文件，并阅读 [此页面](../advanced_config/bootloader_update_from_betaflight.md) 获取烧录说明。

## 构建固件

要 [构建 PX4](../dev_setup/building_px4.md) 为此目标：

```
make holybro_kakuteh7v2_default
```

## 安装 PX4 固件

::: info
KakuteH7v2 支持 PX4 master 和 PX4 v1.14 或更高版本。如果通过 QGroundcontrol 加载预编译固件，必须使用 QGC Daily 或 QGC 版本高于 4.1.7。
在该版本之前需要手动编译并安装固件。
:::

固件可以通过以下常规方式进行手动安装：

- 构建并上传源代码：

  ```
  make holybro_kakuteh7v2_default upload
  ```

- [加载固件](../config/firmware.md) 使用 _QGroundControl_。
  可以使用预编译固件或自定义固件。

::: info
KakuteH7v2 支持 PX4 main 和 v1.14 或更高版本。
:::

## PX4 配置

除了 [基本配置](../config/index.md) 之外，以下参数尤为重要：

| 参数                                                            | 设置                                                                                                                 |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG) | 由于开发板没有内置磁力计，因此应将其禁用。如果连接了外部磁力计，可以启用它。 |

## 串口映射

| 串口   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| USART1 | /dev/ttyS0 | TELEM1                |
| USART3 | /dev/ttyS2 | Debug Console         |
| UART4  | /dev/ttyS3 | GPS1                  |
| USART6 | /dev/ttyS4 | RC SBUS               |
| UART7  | /dev/ttyS5 | ESC telemetry (DShot) |

## 调试端口

### 系统控制台

UART3 的 RX 和 TX 被配置为用作 [系统控制台](../debug/system_console.md)。

### SWD

[SWD接口](../debug/swd_debug.md) (JTAG) 引脚如下：

- `SWCLK`: 测试点2（CPU上的72号引脚）
- `SWDIO`: 测试点3（CPU上的76号引脚）
- `GND`: 如板上标记所示
- `VDD_3V3`: 如板上标记所示