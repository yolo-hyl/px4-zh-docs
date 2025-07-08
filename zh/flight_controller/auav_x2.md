# AUAV-X2 自动驾驶仪（已停产）

<Badge type="info" text="已停产" />

:::warning
该飞控已[停产](../flight_controller/autopilot_experimental.md)，不再商业销售。
:::

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.mrobotics.io/)。
:::

[AUAV<sup>&reg;</sup>](http://www.auav.com/) _AUAV-X2 自动驾驶仪_ 基于 [Pixhawk<sup>&reg;</sup>-project](https://pixhawk.org/) **FMUv2** 开源硬件设计。它在 [NuttX](https://nuttx.apache.org/) OS 上运行 PX4。

![AUAVX2_case2](../../assets/flight_controller/auav_x2/auavx2_case2.jpg)

## 快速概览

- 主系统芯片: [STM32F427](http://www.st.com/web/en/catalog/mmc/FM141/SC1169/SS1577/LN1789)
  - CPU: STM32F427VIT6 ARM 微控制器 - 第3版
  - IO: STM32F100C8T6 ARM 微控制器
- 传感器:
  - Invensense MPU9250 9轴
  - Invensense ICM-20608 6轴
  - MEAS MS5611 气压计
- 尺寸/重量
  - 尺寸: 36mm x 50mm
  - 安装孔: 30.5mm x 30.5mm 3.2mm 直径
  - 重量: 10.9g
- 带反向电压保护的电源OR-ing电路图。必须使用5V电源模块！

## 连接性

- 2.54mm 接口:
- GPS (USART4)
- i2c
- RC 输入
- PPM 输入
- Spektrum 输入
- RSSI 输入
- sBus 输入
- sBus 输出
- 电源输入
- 蜂鸣器输出
- LED 输出
- 8 x 舵机输出
- 6 x 辅助输出
- USART7 (控制台)
- USART8 (OSD)

## 可用性

已停产。
已被 [mRo X2.1](mro_x2.1.md) 取代。
mRobotics 自2017年8月起是 AUAV 产品的分销商。

## 关键链接

- [用户手册](http://arsovtech.com/wp-content/uploads/2015/08/AUAV-X2-user-manual-EN.pdf)
- [DIY Drones文章](http://diydrones.com/profiles/blogs/introducing-the-auav-x2-1-flight-controller)

## 接线指南

![AUAV-X2-basic-setup 3](../../assets/flight_controller/auav_x2/auav_x2_basic_setup_3.png)

![AUAV-X2-basic-setup 2](../../assets/flight_controller/auav_x2/auav_x2_basic_setup_2.jpg)

![AUAV-X2-basic-setup 1](../../assets/flight_controller/auav_x2/auav_x2_basic_setup_1.png)

![AUAV-X2-airspeed-setup 3](../../assets/flight_controller/auav_x2/auav_x2_airspeed_setup_3.png)

## 电路图

该板基于 [Pixhawk项目](https://pixhawk.org/) **FMUv2** 开源硬件设计。

- [FMUv2 + IOv2 电路图](https://raw.githubusercontent.com/PX4/Hardware/master/FMUv2/PX4FMUv2.4.5.pdf) -- 电路图和布局

::: info
作为 CC-BY-SA 3.0 许可的开源硬件设计，所有电路图和设计文件均可在[此处获取](https://github.com/PX4/Hardware)。
:::

## 串口映射

| UART   | 设备       | 端口                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | IO 调试              |
| USART2 | /dev/ttyS1 | TELEM1 (流控制)       |
| USART3 | /dev/ttyS2 | TELEM2 (流控制)       |
| UART4  |            |
| UART7  | CONSOLE    |
| UART8  | SERIAL4    |