# ARK TESEO GPS

[ARK TESEO GPS](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-teseo-gps) 是一款美国制造并符合 NDAA 要求的 [DroneCAN](index.md) [GNSS/GPS](../gps_compass/index.md) L1/L5 GPS、磁力计、气压计、IMU 和蜂鸣器模块。

![ARK TESEO GPS](../../assets/hardware/gps/ark/ark_teseo_gps.jpg)

## 购买渠道

可通过以下渠道订购该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-teseo-gps/)（美国）

## 硬件规格

- [开源原理图与物料清单](https://github.com/ARK-Electronics/ARK_Teseo_GPS)
- 传感器
  - [ST TESEO LIV4F GPS](https://www.st.com/en/positioning/teseo-liv4f.html)
    - L1/L5频段
    - 同时支持多星座多频段GNSS（GPS、伽利略、格洛纳斯、北斗、QZSS）
    - 支持IRNSS星座
    - -162 dBm追踪灵敏度
    - 亚米级定位精度
  - [ST IIS2MDC 磁力计](https://www.st.com/en/mems-and-sensors/iis2mdc.html)
  - [Bosch BMP390 气压计](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/pressure-sensors-bmp390.html)
  - [Invensense ICM-42688-P 六轴IMU](https://invensense.tdk.com/products/motion-tracking/6-axis/icm-42688-p/)
- STM32F412VGH6 MCU
- 双频段（L1/L5）螺旋GPS天线
- 两个Pixhawk标准CAN接口（4针JST GH）
- I2C和时间脉冲/PPS接口（5针JST GH）
- Pixhawk标准调试接口（6针JST SH）
- 供电需求
  - 5V
  - 137mA
- LED指示灯
  - GPS定位状态
  - RGB状态指示
- 美国制造
- NDAA合规

## 引脚定义

### CAN - 4针JST-GH

| 引脚编号 | 信号名称 | 电压 |
| -------- | -------- | ---- |
| 1        | 5V       | 5.0V |
| 2        | CAN_P    | 5.0V |
| 3        | CAN_N    | 5.0V |
| 4        | GND      | GND  |

### I2C + 时间脉冲 - 5针JST-GH

| 引脚编号 | 信号名称          | 电压  |
| -------- | ----------------- | ----- |
| 1        | 5.0V输出（500mA） | 5.0V  |
| 2        | I2C2_SCL          | 3.3V  |
| 3        | I2C2_SDA          | 3.3V  |
| 4        | TIMEPULSE         | 3.3V  |
| 5        | GND               | GND   |

### 调试 - 6针JST-SH

| 引脚编号 | 信号名称    | 电压  |
| -------- | ----------- | ----- |
| 1        | 3.3V        | 3.3V  |
| 2        | USART2_TX   | 3.3V  |
| 3        | USART2_RX   | 3.3V  |
| 4        | FMU_SWDIO   | 3.3V  |
| 5        | FMU_SWCLK   | 3.3V  |
| 6        | GND         | GND   |

## 相关文档

- [ARK TESEO GPS 文档](https://arkelectron.gitbook.io/ark-documentation/sensors/ark-teseo-gps)（ARK官方文档）