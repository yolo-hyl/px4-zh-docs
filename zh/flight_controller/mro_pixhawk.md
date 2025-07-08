# mRo Pixhawk飞行控制器（Pixhawk 1）

:::warning
PX4不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.mrobotics.io/)。
:::

_mRo Pixhawk<sup>&reg;</sup>_ 是原始 [Pixhawk 1](../flight_controller/pixhawk.md) 的硬件兼容版本。它在 [NuttX](https://nuttx.apache.org/) OS 上运行 PX4。

:::tip
该控制器可以作为 3DR<sup>&reg;</sup> [Pixhawk 1](../flight_controller/pixhawk.md) 的即插即用替代品。
主要区别在于它基于 [Pixhawk-project](https://pixhawk.org/) **FMUv3** 开放硬件设计，修复了原版 Pixhawk 1 闪存限制在 1MB 的错误。
:::

![mRo Pixhawk 图像](../../assets/flight_controller/mro/mro_pixhawk.jpg)

与 PX4 一起使用的装配/设置说明如下：[Pixhawk 接线快速入门](../assembly/quick_start_pixhawk.md)

:::tip
此自动驾驶仪由 PX4 维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 核心特性

- 微处理器：
  - 32位STM32F427 Cortex<sup>&reg;</sup> M4核心（带FPU）
  - 168 MHz/256 KB RAM/2 MB Flash
  - 32位STM32F100安全协处理器
  - 24 MHz/8 KB RAM/64 KB Flash
- 传感器：
  - ST Micro L3GD20 3轴16位陀螺仪
  - ST Micro LSM303D 3轴14位加速度计/磁力计
  - Invensense<sup>&reg;</sup> MPU 6000 3轴加速度计/陀螺仪
  - MEAS MS5611气压计
- 接口：
  - 5x UART（串口），1个高功率接口，2x带硬件流控制
  - 2x CAN
  - Spektrum DSM / DSM2 / DSM-X® 卫星兼容输入，支持至DX8（不支持DX9及以上）
  - Futaba<sup>&reg;</sup> S.BUS 兼容输入和输出
  - PPM总和信号
  - RSSI（PWM或电压）输入
  - I2C
  - SPI
  - 3.3和6.6V ADC输入
  - 外部microUSB端口
- 电源系统：

  - 理想二极管控制器带自动故障转移
  - 伺服总线高功率（7 V）和高电流支持
  - 所有外设输出过流保护，所有输入ESD保护

- 重量与尺寸：
  - 重量：38g（1.31盎司）
  - 宽度：50mm（1.96英寸）
  - 厚度：15.5mm（0.613英寸）
  - 长度：81.5mm（3.21英寸）

## 可用性

- [Bare Bones](https://store.mrobotics.io/Genuine-PixHawk-1-Barebones-p/mro-pixhawk1-bb-mr.htm) - 仅电路板（适合作为3DR Pixhawk替代品）
- [mRo Pixhawk 2.4.6 Essential Kit!](https://store.mrobotics.io/Genuine-PixHawk-Flight-Controller-p/mro-pixhawk1-minkit-mr.htm) - 除遥测无线电外的所有组件
- [mRo Pixhawk 2.4.6 Cool Kit! (Limited edition)](https://store.mrobotics.io/product-p/mro-pixhawk1-fullkit-mr.htm) - 包括遥测无线电在内的所有必需组件

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适硬件时，_QGroundControl_ 会自动安装预构建的固件。
:::

要为该目标[构建PX4](../dev_setup/building_px4.md)：

```
make px4_fmu-v3_default
```

## 调试端口

参见 [3DR Pixhawk 1 > 调试端口](../flight_controller/pixhawk.md#debug-ports)

## 引脚分配

参见 [3DR Pixhawk 1 > 引脚分配](../flight_controller/pixhawk.md#pinouts)

## 串口映射

| UART   | 设备     | 端口                  |
| ------ | -------- | --------------------- |
| UART1  | /dev/ttyS0 | IO调试              |
| USART2 | /dev/ttyS1 | 遥测1（流量控制）     |
| USART3 | /dev/ttyS2 | 遥测2（流量控制）     |
| UART4  |          |
| UART7  | CONSOLE  |
| UART8  | SERIAL4  |

<!-- 注：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口信息 -->

## 电路图

该电路板基于 [Pixhawk-project](https://pixhawk.org/) **FMUv3** 开放硬件设计。

- [FMUv3 电路图](https://github.com/PX4/Hardware/raw/master/FMUv3_REV_D/Schematic%20Print/Schematic%20Prints.PDF) -- 电路图和布局

::: info
作为 CC-BY-SA 3.0 授权的开放硬件设计，所有电路图和设计文件均[可用](https://github.com/PX4/Hardware)。
:::