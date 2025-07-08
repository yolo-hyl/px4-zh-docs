# Holybro Kakute F7（已停产）

<Badge type="info" text="已停产" />

:::warning
PX4 不生产任何自动驾驶仪设备。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

Holybro 的 _Kakute F7_ 是一款专为竞速无人机设计的飞控板。

<img src="../../assets/flight_controller/kakutef7/board.jpg" width="400px" title="Kakute F7" />

::: info
此飞控器获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 核心特性

- 主系统芯片：[STM32F745VGT6](https://www.st.com/en/microcontrollers-microprocessors/stm32f745vg.html)
  - CPU：216 MHz ARM Cortex M7 带单精度FPU
  - RAM：320 KB SRAM
  - FLASH：1 MB
- 标准竞速板型：36x36 mm 标准 30.5 mm 孔距
- ICM20689 加速度计/陀螺仪（软安装）
- BMP280 气压计
- microSD（用于日志记录）
- 6 个 UART
- 1 个 I2C 总线
- 6 个 PWM 输出
- 内置 OSD 芯片（通过 SPI 的 AB7456）

## 购买渠道

该板可从以下商店购买（例如）：

- [getfpv](https://www.getfpv.com/holybro-kakute-f7-tekko32-f3-metal-65a-4-in-1-esc-combo.html)

:::tip
_Kakute F7_ 专为与 _Tekko32_ 四合一电调配合使用而设计，它们可以组合购买。
:::

## 连接器与引脚

这是 _Kakute F7_ 的丝印图，显示板子的顶部：

![Kakute F7 丝印图](../../assets/flight_controller/kakutef7/silk.png)

| 引脚      | 功能                                                             | PX4 默认设置         |
| -------- | -------------------------------------------------------------------- | ------------------- |
| B+       | 电池正极电压（2S-6S）                                     |                     |
| 5V       | 5V 输出（最大 2A）                                                   |                     |
| VO       | 视频输出到视频发射器                                    |                     |
| VI       | 来自 FPV 摄像头的视频输入                                          |                     |
| G 或 GND | 地线                                                               |                     |
| SDA, SCL | I2C 接口（用于外设）                                     |                     |
| R1, T1   | UART1 接收和发送                                                      | TELEM1              |
| R2, T2   | UART2 接收和发送                                                      | TELEM2              |
| R3, T3   | UART3 接收和发送                                                      | NuttX 调试控制台 |
| R4, T4   | UART4 接收和发送                                                      | GPS1                |
| R6, T6   | UART6 接收和发送                                                      | RC 接口             |
| R7, T7   | UART7 接收和发送（接收端位于插头处，用于四合一电调） | DShot 茶仪         |
| LED      | 可寻址 WS2182 LED 信号线（未经过测试）                      |                     |
| Buz-     | 压电蜂鸣器负极（蜂鸣器正极连接到 5V 引脚）    |                     |
| 3V3      | 3.3V 输出（最大 200 mA）                                             |                     |
| M1 到 M4 | 电机信号输出（位于插头处，用于四合一电调）        |                     |
| M5, M6   | 额外电机信号输出（位于板子侧面）           |                     |
| RSI      | 来自接收机的模拟 RSSI（0-3.3V）输入                             |                     |
| Boot     | 引导程序按钮                                                    |                     |

<a id="bootloader"></a>

## PX4 引导程序更新

该板预装了 [Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装 PX4 固件之前，必须先烧录 _PX4 引导程序_。
下载 [kakutef7_bl.hex](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/kakutef7/kakutef7_bl_0b3fbe2da0.hex) 引导程序二进制文件，并阅读 [此页面](../advanced_config/bootloader_update_from_betaflight.md) 获取烧录说明。

## 构建固件

要[构建 PX4](../dev_setup/building_px4.md) 为此目标：

```
make holybro_kakutef7_default
```

## 安装 PX4 固件

固件可以通过以下任何常规方式安装：

- 构建并上传源代码
  ```
  make holybro_kakutef7_default upload
  ```
- 使用 _QGroundControl_ [加载固件](../config/firmware.md)。
  您可以使用预构建固件或自定义固件。

## 配置

如果您使用 Betaflight/Cleanflight 电机分配的四合一电调，可以使用 [执行器](../config/actuators.md) 配置界面正确设置电机顺序。

除了 [基本配置](../config/index.md)，以下参数也很重要：

| 参数                                                            | 设置                                                                                                                 |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| [SYS_HAS_MAG](../advanced_config/parameter_reference.md#SYS_HAS_MAG) | 由于板子没有内置磁力计，应禁用此参数。如果连接外部磁力计，可以启用。 |

## 串口映射

| UART   | 设备     | 端口                  |
| ------ | ---------- | --------------------- |
| USART1 | /dev/ttyS0 | TELEM1                |
| USART2 | /dev/ttyS1 | TELEM2                |
| USART3 | /dev/ttyS2 | 调试控制台         |
| UART4  | /dev/ttyS3 | GPS1                  |
| USART6 | /dev/ttyS4 | RC SBUS               |
| UART7  | /dev/ttyS5 | 电调遥测（DShot） |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->

## 调试端口

### 系统控制台

UART3 的 RX 和 TX 被配置为 [系统控制台](../debug/system_console.md)。

### SWD

[SWD 接口](../debug/swd.md) 的配置如下：

| 引脚 | 功能       |
| ---- | ---------- |
| SWDIO | 数据 I/O  |
| SWCLK | 时钟信号  |
| GND  | 地线       |

### JTAG/SWD 转换器

使用 ST-Link v2 或兼容的 JTAG/SWD 转换器连接到以下引脚：

| 转换器引脚 | Kakute F7 引脚 |
| ---------- | -------------- |
| SWDIO      | SWDIO          |
| SWCLK      | SWCLK          |
| GND        | GND            |

## 系统控制台

### 串口调试

使用以下命令通过串口连接到飞控：

```bash
screen /dev/ttyUSB0 115200
```

## 电源管理

### 电池电压监测

通过以下步骤校准电压监测：

1. 在 QGroundControl 中进入 **Setup > Power > Voltage Calibration**。
2. 根据电池类型选择校准系数。
3. 保存并重新启动飞控。

## 故障排除

### 常见问题

| 问题 | 解决方案 |
|----|------|
| 无法连接到 QGroundControl | 检查 USB 连接并确保驱动程序已安装 |
| 电机不转 | 检查电机线缆连接和电调设置 |
| 飞行不稳定 | 校准 IMU 并检查 PID 参数 |

## 维护

### 固件更新

定期检查 PX4 官方网站获取最新固件更新。

### 硬件维护

- 定期清洁板子上的灰尘
- 检查所有连接器是否牢固
- 避免在极端温度下使用

## 许可证

此硬件设计遵循 [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/)。