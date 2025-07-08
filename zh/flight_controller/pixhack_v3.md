# Pixhack V3

:::warning
PX4不制造此(或任何)自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

CUAV _Pixhack V3_ 飞行控制器板是一款面向商业系统制造商的灵活自动驾驶仪。

该板是 SOLO Pixhawk<sup>&reg;</sup> 2 (PH2) 飞行控制器的变种，而 SOLO Pixhawk 2 本身基于 [Pixhawk-project](https://pixhawk.org/) **FMUv3** 开源硬件设计。  
它运行 PX4 操作系统在 [NuttX](https://nuttx.apache.org/) 上，与 PX4 或 ArduPilot<sup>&reg;</sup> (APM) 固件完全兼容。

_Pixhack V3_ 相比原始设计有显著改进，包括更好的接口布局、振动阻尼系统和恒温系统。

![Pixhack v3](../../assets/flight_controller/pixhack_v3/pixhack_v3_157_large_default.jpg)

::: info
此飞行控制器获得[厂商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 快速摘要

- 微处理器:
  - STM32F427
  - STM32F100 (安全处理器)
- 传感器:
  - 加速度计 (3): LS303D, MPU6000, MPU9250/hmc5983
  - 陀螺仪 (3): L3GD20, MPU6000, MPU9250
  - 磁力计 (2): LS303D, MPU9250
  - 气压计 (2): MS5611 X2
- 接口:
  - MAVLink UART (2)
  - GPS UART (2)
  - DEBUG UART (1)
  - RC 输入 (支持 PPM, SBUS, DSM/DSM2)
  - RSSI 输入: PWM 或 3.3ADC
  - I2C (2)
  - CAN 总线 (1)
  - ADC 输入: 3.3V X1 , 6.6V X1
  - PWM 输出: 8 PWM IO + 4 IO
- 电源系统:
  - PM 电源输入: 4.5 ~ 5.5 V
  - USB 电源输入: 5.0 V +- 0.25v
- 重量与尺寸:
  - 重量: 63g
  - 宽度: 68mm
  - 厚度: 17mm
  - 长度: 44mm
- 其他特性:
  - 工作温度: -20 ~ 60°C

## 可用性

该板可从以下地址购买:

- [store.cuav.net](http://store.cuav.net/index.php?id_product=8&id_product_attribute=0&rewrite=pixhack-v3-autopilot&controller=product&id_lang=3)
- [leixun.aliexpress.com/store](https://leixun.aliexpress.com/store)

## 固件构建

:::tip
大多数用户无需构建此固件！
当连接相应硬件时，_QGroundControl_ 会自动预构建并安装固件。
:::

要为本目标[构建 PX4](../dev_setup/building_px4.md):

```
make px4_fmu-v3_default
```

## 引脚图和原理图

- [Documentation/wiring guides](http://doc.cuav.net/flight-controller/pixhack/en/pixhack-v3.html)

## 串口映射

| UART   | 设备     | 端口                  |
| ------ | -------- | --------------------- |
| UART1  | /dev/ttyS0 | IO 调试              |
| USART2 | /dev/ttyS1 | TELEM1 (流量控制)     |
| USART3 | /dev/ttyS2 | TELEM2 (流量控制)     |
| UART4  |          |
| UART7  | 控制台    |
| UART8  | SERIAL4  |