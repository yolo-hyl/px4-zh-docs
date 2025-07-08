# ARK Pi6X Flow

[ARK Pi6X Flow](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow) 集成了树莓派计算模块4（CM4）载板、[ARKV6X飞行控制器](../flight_controller/ark_v6x.md)、[ARK Flow传感器](../dronecan/ark_flow.md)、[ARK PAB电源模块](../power_module/ark_pab_power_module.md)和4合1电调，全部安装在一块紧凑的电路板上。

![ARK Pi6X Flow飞行控制器](../../assets/flight_controller/ark_pi6x_flow/ark_pi6xflow.jpg)

## 购买途径

从以下渠道订购该模块：

- [ARK Electronics](https://arkelectron.com/product/ark-pi6x-flow/) (US)

## 关键组件

### Raspberry Pi Compute Module 4 载板

- 飞控固件: PX4
- 特性:
  - MicroSD 插槽（适用于 CM4 Lite）
  - Micro HDMI
  - 以太网
  - USB C 主机
  - 2x USB JST GH
  - 2x MIPI CSI（22针FFC）
  - 编程用micro USB
  - I2C, UART
  - LED灯带GPIO
  - 通用GPIO
  - 控制台
  - CPU风扇

### 飞控系统

- 通信:
  - TELEM（UART）
  - 遥控输入（UART）
  - GPS（UART, I2C）
  - CAN
- 控制与监控:
  - 8路PWM输出
  - 蜂鸣器
  - 调试（SWD, UART控制台）
  - 数据存储用MicroSD插槽

### 传感器

- 2x ICM-42688-P 加速度计/陀螺仪（含加热器）
- PAW3902 光流传感器
- AFBR-S50LV85D 30米距离传感器
- ST IIS2MDC 磁力计
- BMP390 气压计
- INA226 电压/电流监测器

### 美国制造并符合NDAA要求## 供电要求

- 电池输入：最高支持 6S/25.2V（实际需求取决于使用情况和外设）## 尺寸与重量

- 尺寸：91.5mm x 56mm（不含Pi CM4和ESC）
- 重量：
  - 41g（无Pi CM4、Pi SD和ESC）
  - 53g（含Pi CM4和Pi SD，不含ESC）## 附加信息

- 包含：飞控MicroSD卡
- 套装选项包含：
  - Raspberry Pi Compute Module 4 with WiFi, 4GB RAM Lite – CM4104000
  - SanDisk 128GB High Endurance Video MicroSDXC（预装[ARK-OS](https://github.com/ARK-Electronics/ARK-OS)）## 烧录指南

[ARK Pi6X 烧录指南](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow/flashing-guide)

## 引脚定义

![ARK Pi6X Flow Pinout](../../assets/flight_controller/ark_pi6x_flow/ark_pi6xflow_pinout.png)

### PWM UART4 - 11 Pin JST-GH

| 引脚编号 | 信号名称      | 电压   |
| :------- | :------------ | :----- |
| 1        | FMU_CH1_EXT   | 3.3V   |
| 2        | FMU_CH2_EXT   | 3.3V   |
| 3        | FMU_CH3_EXT   | 3.3V   |
| 4        | FMU_CH4_EXT   | 3.3V   |
| 5        | FMU_CH5_EXT   | 3.3V   |
| 6        | FMU_CH6_EXT   | 3.3V   |
| 7        | FMU_CH7_EXT   | 3.3V   |
| 8        | FMU_CH8_EXT   | 3.3V   |
| 9        | UART4_TX_EXT  | 3.3V   |
| 10       | UART4_RX_EXT  | 3.3V   |
| 11       | GND           | GND    |

### RC - 4 Pin JST-GH

| 引脚编号 | 信号名称        | 电压   |
| :------- | :-------------- | :----- |
| 1        | VDD_5V_SBUS_RC  | 5.0V   |
| 2        | USART6_RX_IN_EXT| 3.3V   |
| 3        | USART6_TX_OUT_EXT| 3.3V  |
| 4        | GND             | GND    |

### CAM0 - 22 Pin

0.5mm FFC 0545482271

| 引脚编号 | 信号名称 | 电压   |
| :------- | :------- | :----- |
| 1        | GND      | GND    |
| 2        | CAM0_D0_N| 1.2V   |
| 3        | CAM0_D0_P| 1.2V   |
| 4        | GND      | GND    |
| 5        | CAM0_D1_N| 1.2V   |
| 6        | CAM0_D1_P| 1.2V   |
| 7        | GND      | GND    |
| 8        | CAM0_C_N | 1.2V   |
| 9        | CAM0_C_P | 1.2V   |
| 10       | GND      | GND    |
| 11       | NC       | NC     |
| 12       | NC       | NC     |
| 13       | GND      | GND    |
| 14       | NC       | NC     |
| 15       | NC       | NC     |
| 16       | GND      | GND    |
| 17       | CAM_GPIO | 3.3V   |
| 18       | NC       | NC     |
| 19       | GND      | GND    |
| 20       | ID_SC    | 3.3V   |
| 21       | ID_SD    | 3.3V   |
| 22       | 3.3V_RPI | 3.3V   |

### CAM1 - 22 Pin 0.5mm FFC 0545482271

| 引脚编号 | 信号名称   | 电压   |
| :------- | :--------- | :----- |
| 1        | GND        | GND    |
| 2        | CAM1_D0_N  | 1.2V   |
| 3        | CAM1_D0_P  | 1.2V   |
| 4        | GND        | GND    |
| 5        | CAM1_D1_N  | 1.2V   |
| 6        | CAM1_D1_P  | 1.2V   |
| 7        | GND        | GND    |
| 8        | CAM1_C_N   | 1.2V   |
| 9        | CAM1_C_P   | 1.2V   |
| 10       | GND        | GND    |
| 11       | CAM1_D2_N  | 1.2V   |
| 12       | CAM1_D2_P  | 1.2V   |
| 13       | GND        | GND    |
| 14       | CAM1_D3_N  | 1.2V   |
| 15       | CAM1_D3_P  | 1.2V   |
| 16       | GND        | GND    |
| 17       | CAM_GPIO   | 3.3V   |
| 18       | NC         | NC     |
| 19       | GND        | GND    |
| 20       | SCL0       | 3.3V   |
| 21       | SDA0       | 3.3V   |
| 22       | 3.3V_RPI   | 3.3V   |## 另请参阅

- [ARK Pi6X Flow Documentation](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow) (ARK Docs)