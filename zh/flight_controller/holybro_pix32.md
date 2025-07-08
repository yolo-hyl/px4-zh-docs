# Holybro pix32 飞控（已停产）

<Badge type="info" text="已停产" />

:::warning
PX4 不生产任何自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

Holybro<sup>&reg;</sup> [pix32 飞控](https://holybro.com/collections/autopilot-flight-controllers/products/pix32pixhawk-flight-controller)（也被称为 "Pixhawk 2"，之前称为 HKPilot32）基于 [Pixhawk<sup>&reg;</sup>-project](https://pixhawk.org/) **FMUv2** 开源硬件设计。
该电路板基于 Pixhawk 2.4.6 硬件版本。
它在 [NuttX](https://nuttx.apache.org/) OS 上运行 PX4 飞行栈。

![pix32](../../assets/flight_controller/holybro_pix32/pix32_hero.jpg)

作为 CC-BY-SA 3.0 授权的开源硬件设计，原理图和设计文件应[在此获取](https://github.com/PX4/Hardware)。

:::tip
Holybro pix32 与 [3DR Pixhawk 1](../flight_controller/pixhawk.md) 软件兼容。
它不兼容接插件，但物理上非常接近 3DR Pixhawk 或 mRo Pixhawk。
:::

::: info
此飞控受[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 核心特性

- 主系统芯片：[STM32F427](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)
  - CPU：32 位 STM32F427 Cortex<sup>&reg;</sup> M4 核心带 FPU
  - RAM：168 MHz/256 KB
  - Flash：2 MB
- 失效保护系统芯片：STM32F103
- 传感器：
  - ST Micro L3GD20 3 轴 16 位陀螺仪
  - ST Micro LSM303D 3 轴 14 位加速度计/磁力计
  - Invensense<sup>&reg;</sup> MPU 6000 3 轴加速度计/陀螺仪
  - MEAS MS5611 气压计
- 尺寸/重量
  - 尺寸：81x44x15mm
  - 重量：33.1g
- GPS：u-blox<sup>&reg;</sup> 超精度 Neo-7M 带指南针
- 输入电压：2~10s（7.4~37V）

### 连接性

- 1x I2C
- 2x CAN
- 3.3 和 6.6V ADC 输入
- 5x UART（串口），1 个高功率，2x 带硬件流控制
- Spektrum DSM / DSM2 / DSM-X® 卫星兼容输入，最高支持 DX8（DX9 及以上不支持）
- Futaba<sup>&reg;</sup> S.BUS 兼容输入和输出
- PPM 汇总信号
- RSSI（PWM 或电压）输入
- SPI
- 外部 microUSB 端口
- Molex PicoBlade 连接器

## 购买渠道

[shop.holybro.com](https://holybro.com/collections/autopilot-flight-controllers/products/pix32pixhawk-flight-controller)

### 附件

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [Hobbyking<sup>&reg;</sup> 无线数传](https://hobbyking.com/en_us/apm-pixhawk-wireless-wifi-radio-module.html)
- [HolyBro SiK 数传电台（EU 433 MHz，US 915 MHz）](../telemetry/holybro_sik_radio.md)

## 固件编译

:::tip
大多数用户不需要编译此固件！
当连接适当硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要为本目标[编译 PX4](../dev_setup/building_px4.md)：

```
make px4_fmu-v2_default
```

## 调试端口

参见 [3DR Pixhawk 1 > 调试端口](../flight_controller/pixhawk.md#debug-ports)。

## 引脚分配和原理图

该电路板基于 [Pixhawk 项目](https://pixhawk.org/) **FMUv2** 开源硬件设计。

- [FMUv2 + IOv2 原理图](https://raw.githubusercontent.com/PX4/Hardware/master/FMUv2/PX4FMUv2.4.5.pdf) -- 原理图和布局

::: info
作为 CC-BY-SA 3.0 授权的开源硬件设计，所有原理图和设计文件[均可获取](https://github.com/PX4/Hardware)。
:::

## 串口映射

| UART   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | IO 调试              |
| USART2 | /dev/ttyS1 | TELEM1（流控制）     |
| USART3 | /dev/ttyS2 | TELEM2（流控制）     |
| UART4  |            |
| UART7  | CONSOLE    |
| UART8  | SERIAL4    |

<!-- 注意：通过 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 获取端口 -->