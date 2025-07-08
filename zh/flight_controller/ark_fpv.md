# ARK FPV 飞控

:::warning
PX4 不制造此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://arkelectron.com/contact-us/)。
:::

美国制造的 ARK FPV 飞控基于 [ARKV6X](https://arkelectron.com/product/arkv6x/) 设计，采用 30.5mm 标准安装孔距，支持 3-12s 电池输入，并提供稳压 12V 2A 输出用于图传和载荷。

![ARK FPV 主图](../../assets/flight_controller/arkfpv/ark_fpv.jpg)

:::info
此飞控[获得制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 购买渠道

从 [Ark Electronics](https://arkelectron.com/product/arkv6x/) 购买（美国）

## 文档

查看 [Ark Electronics GitBook](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-fpv)

## 传感器

- [Invensense IIM-42653 工业IMU](https://invensense.tdk.com/products/motion-tracking/6-axis/iim-42653/)
- [Bosch BMP390 气压计](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp390/)
- [ST IIS2MDC 电子罗盘](https://www.st.com/en/magnetic-sensors/iis2mdc.html)

## 微处理器

- [STM32H743IIK6 MCU](https://www.st.com/en/microcontrollers-microprocessors/stm32h743ii.html)
  - 480 MHz
  - 2 MB 闪存
  - 1 MB RAM

## 连接器

- USB-C
  - VBUS 输入，USB
- PWM
  - VBAT 输入，模拟电流输入，遥测 RX，4x PWM
  - JST-GH 8针
- PWM 额外
  - 5x PWM
  - JST-SH 6针
- RC 输入
  - 5V 输出，UART
  - JST-GH 4针
- 电源辅助
  - 12V 输出，VBAT 输入/输出
  - JST-GH 3针
- 遥测
  - 5V 输出，带流量控制的 UART
  - JST-GH 6针
- GPS
  - 5V 输出，UART，I2C
  - JST-GH 6针
- CAN
  - 5V 输出，CAN
  - JST-GH 4针
- 图传
  - 12V 输出，UART TX/RX，UART RX
  - JST-GH 6针
- SPI（OSD 或外部IMU）
  - 5V 输出，SPI
  - JST-SH 8针
- 调试
  - 3.3V 输出，UART，SWD
  - JST-SH 6针

## 电源需求

- 5.5V - 54V
- 500 毫安（300 毫安主系统，200 毫安加热器）

## 其他信息

- 重量：7.5 克（含MicroSD卡）
- 尺寸：3.6 x 3.6 x 0.8 厘米
- 美国制造 - 符合NDAA
- 加热器：1W 用于极端寒冷环境下的传感器加热
- LED 指示灯
- MicroSD 插槽

## 引脚图

参见 [DS-10 Pixhawk 自动驾驶总线标准](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-fpv/pinout)