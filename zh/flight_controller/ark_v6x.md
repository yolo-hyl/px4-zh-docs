# ARK Electronics ARKV6X

:::warning
PX4 并不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://arkelectron.com/contact-us/)。
:::

美国制造的 [ARKV6X](<(https://arkelectron.gitbook.io/ark-documentation/flight-controllers/arkv6x)>) 飞行控制器基于 [FMUV6X 和 Pixhawk Autopilot Bus 开源标准](https://github.com/pixhawk/Pixhawk-Standards)。

该控制器具备三同步惯性测量单元（IMUs），支持数据平均、投票和滤波处理。
Pixhawk Autopilot Bus（PAB）外形规格使 ARKV6X 可用于任何 [PAB 兼容载板](../flight_controller/pixhawk_autopilot_bus.md)，例如 [ARK Pixhawk Autopilot Bus 载板](../flight_controller/ark_pab.md)。

![ARKV6X 主视图](../../assets/flight_controller/arkv6x/ark_v6x_front.jpg)

::: info
此飞行控制器为[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)型号。
:::

## 购买渠道

从 [Ark Electronics](https://arkelectron.com/product/arkv6x/) 订购（美国）

## 传感器

- [双 Invensense ICM-42688-P IMUs](https://invensense.tdk.com/products/motion-tracking/6-axis/icm-42688-p/)
- [Invensense IIM-42652 工业IMU](https://invensense.tdk.com/products/smartindustrial/iim-42652/)
- [Bosch BMP390 气压计](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp390/)
- [Bosch BMM150 磁力计](https://www.bosch-sensortec.com/products/motion-sensors/magnetometers/bmm150/)

## 微处理器

- [STM32H743IIK6 MCU](https://www.st.com/en/microcontrollers-microprocessors/stm32h743ii.html)
  - 480MHz
  - 2MB 闪存
  - 1MB 闪存

## 其他特性

- FRAM
- [Pixhawk Autopilot Bus (PAB) 外形规格](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)
- LED 指示灯
- MicroSD 插槽
- 美国制造
- 设计包含 1W 加热器。在极端条件下可保持传感器温度

## 电源需求

- 5V
- 500mA
  - 300ma 用于主系统
  - 200ma 用于加热器

## 额外信息

- 重量：5.0 g
- 尺寸：3.6 x 2.9 x 0.5 cm

## 引脚布局

ARKV6X 的引脚布局请参见 [DS-10 Pixhawk Autopilot Bus 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf)

## 串口映射

| UART   | 设备       | 端口          |
| ------ | ---------- | ------------- |
| USART1 | /dev/ttyS0 | GPS           |
| USART2 | /dev/ttyS1 | TELEM3        |
| USART3 | /dev/ttyS2 | 调试控制台    |
| UART4  | /dev/ttyS3 | UART4 & I2C   |
| UART5  | /dev/ttyS4 | TELEM2        |
| USART6 | /dev/ttyS5 | PX4IO/RC      |
| UART7  | /dev/ttyS6 | TELEM1        |
| UART8  | /dev/ttyS7 | GPS2          |

## 构建固件

```sh
make ark_fmu-v6x_default
```

## 参见

- [ARK Electronics ARKV6X](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/arkv6x) (ARK 官方文档)