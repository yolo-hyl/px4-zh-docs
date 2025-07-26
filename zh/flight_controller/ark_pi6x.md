

# ARK Pi6X Flow

[ARK Pi6X Flow](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow) 集成了树莓派计算模块4 (CM4) 托架、[ARKV6X飞控](../flight_controller/ark_v6x.md)、[ARK Flow传感器](../dronecan/ark_flow.md)、[ARK PAB电源模块](../power_module/ark_pab_power_module.md) 和4合1电调，所有组件集成在单块紧凑的电路板上。

![ARK Pi6X Flow飞控](../../assets/flight_controller/ark_pi6x_flow/ark_pi6xflow.jpg)

## 购买地点

通过以下链接购买此模块：

- [ARK Electronics](https://arkelectron.com/product/ark-pi6x-flow/) (美国)

## 关键组件

### Raspberry Pi Compute Module 4 载板

- 飞控固件: PX4
- 特性:
  - MicroSD卡槽 (用于 CM4 Lite)
  - Micro HDMI
  - 以太网
  - USB C 主机
  - 2x USB JST GH
  - 2x MIPI CSI (22针 FFC)
  - 编程 micro USB
  - I2C, UART
  - LED灯带 GPIO
  - 通用 GPIO
  - 控制台
  - CPU风扇

### 自动驾驶仪

- 通信：
  - 遥测（UART）
  - 遥控输入（UART）
  - GPS（UART，I2C）
  - CAN
- 控制与监控：
  - 8路PWM输出
  - 蜂鸣器
  - 调试（SWD，UART控制台）
  - MicroSD卡槽用于数据存储

### 传感器

- 2个ICM-42688-P 加速度计/陀螺仪（含加热器）  
- PAW3902 光流传感器  
- AFBR-S50LV85D 30米距离传感器  
- ST IIS2MDC 磁力计  
- BMP390 气压计  
- INA226 电压/电流监测器

### 美国制造并符合NDAA要求

## 电源需求

- 电池输入：最高6S/25.2V（实际需求取决于使用情况和外设）

## 尺寸与重量

- 尺寸: 91.5mm x 56mm (不含 Pi CM4 和 ESC)
- 重量:
  - 41g (不含 Pi CM4、Pi SD 和 ESC)
  - 53g (套装含 Pi CM4 和 Pi SD，不含 ESC)

## 附加信息

- 含配件：飞控 MicroSD
- 套件选项包含：
  - Raspberry Pi Compute Module 4 with WiFi, 4GB RAM Lite – CM4104000
  - SanDisk 128GB 高耐久性视频 MicroSDXC 预装 [ARK-OS](https://github.com/ARK-Electronics/ARK-OS)

## 固件烧录指南

[ARK Pi6X 固件烧录指南](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow/flashing-guide)

## 引脚分配

![ARK Pi6X Flow Pinout](../../assets/flight_controller/ark_pi6x_flow/ark_pi6xflow_pinout.png)

### PWM UART4 - 11针JST-GH

| 针脚编号 | 信号名称    | 电压   |
| :------- | :---------- | :----- |
| 1        | FMU_CH1_EXT | 3.3V   |
| 2        | FMU_CH2_EXT | 3.3V   |
| 3        | FMU_CH3_EXT | 3.3V   |
| 4        | FMU_CH4_EXT | 3.3V   |
| 5        | FMU_CH5_EXT | 3.3V   |
| 6        | FMU_CH6_EXT | 3.3V   |
| 7        | FMU_CH7_EXT | 3.3V   |
| 8        | FMU_CH8_EXT | 3.3V   |
| 9        | UART4_TX_EXT| 3.3V   |
| 10       | UART4_RX_EXT| 3.3V   |
| 11       | GND         | GND    |

### RC - 4针JST-GH

| 引脚编号 | 信号名称          | 电压   |
| :------- | :---------------- | :----- |
| 1        | VDD_5V_SBUS_RC    | 5.0V   |
| 2        | USART6_RX_IN_EXT  | 3.3V   |
| 3        | USART6_TX_OUTPUT_EXT | 3.3V |
| 4        | GND               | 地     |

### CAN - 4针JST-GH

| 引脚号 | 信号名称       | 电压 |
| :----- | :------------- | :--- |
| 1      | VDD_5V高功率   | 5.0V |
| 2      | CAN1_P         | 5.0V |
| 3      | CAN1_N         | 5.0V |
| 4      | 地             | GND  |

### GPS - 6针JST-GH

| 引脚编号 | 信号名称          | 电压    |
| :------- | :---------------- | :------ |
| 1        | VDD_5V_HIPOWER    | 5.0V    |
| 2        | USART1_TX_GPS1_EXT| 3.3V    |
| 3        | USART1_RX_GPS1_EXT| 3.3V    |
| 4        | I2C1_SCL_GPS1_EXT | 3.3V    |
| 5        | I2C1_SDA_GPS1_EXT | 3.3V    |
| 6        | GND               | GND     |

### Telem1 - 6针JST-GH

| 针脚编号 | 信号名称              | 电压   |
| :------ | :------------------- | :---- |
| 1       | VDD_5V_HIPOWER       | 5.0V  |
| 2       | UART7_TX_TELEM1_EXT  | 3.3V  |
| 3       | UART7_RX_TELEM1_EXT  | 3.3V  |
| 4       | UART7_CTS_TELEM1_EXT | 3.3V  |
| 5       | UART7_RTS_TELEM1_EXT | 3.3V  |
| 6       | GND                  | GND   |

### 飞控调试 - 10针JST-SH

| 引脚号 | 信号名称            | 电压   |
| :----- | :------------------ | :----- |
| 1      | 3.3V_FMU            | 3.3V   |
| 2      | USART4发送调试      | 3.3V   |
| 3      | USART4接收调试      | 3.3V   |
| 4      | FMU_SWDIO           | 3.3V   |
| 5      | FMU_SWCLK           | 3.3V   |
| 6      | SPI6_SCK_EXTERNAL1  | 3.3V   |
| 7      | NFC_GPIO            | 3.3V   |
| 8      | PD15                | 3.3V   |
| 9      | FMU_NRST            | 3.3V   |
| 10     | GND                 | GND    |

### Pi I2C1 - 4针 JST-GH

| 引脚编号 | 信号名称       | 电压   |
| :------- | :------------- | :----- |
| 1        | 5.0V           | 5.0V   |
| 2        | I2C1_SCL_EXT   | 3.3V   |
| 3        | I2C1_SDA_EXT   | 3.3V   |
| 4        | GND            | GND    |

### Pi UART3 - 6针JST-GH

| 引脚编号 | 信号名称     | 电压   |
| :------- | :----------- | :----- |
| 1        | 5.0V         | 5.0V   |
| 2        | UART3_TX_EXT | 3.3V   |
| 3        | UART3_RX_EXT | 3.3V   |
| 4        | UART3_CTS_EXT| 3.3V   |
| 5        | UART3_RTS_EXT| 3.3V   |
| 6        | GND          | GND    |

### Pi ETH - 4 针 JST-GH

| 引脚编号 | 信号名称 | 电压 |
| :--------- | :---------- | :------ |
| 1          | ETH_RD_N    | 3.3V    |
| 2          | ETH_RD_P    | 3.3V    |
| 3          | ETH_TD_N    | 3.3V    |
| 4          | ETH_TD_P    | 3.3V    |

### Pi LED灯带 - 8针JST-GH

| 针脚编号 | 信号名称     | 电压   |
| :------- | :----------- | :----- |
| 1        | 5.0V         | 5.0V   |
| 2        | 5.0V         | 5.0V   |
| 3        | GPIO12_EXT   | 3.3V   |
| 4        | GPIO16_EXT   | 3.3V   |
| 5        | GPIO17_EXT   | 3.3V   |
| 6        | GPIO20_EXT   | 3.3V   |
| 7        | GND          | GND    |
| 8        | GND          | GND    |

### Pi GPIO - 6针JST-GH

| 引脚编号 | 信号名称    | 电压   |
| :------ | :---------- | :----- |
| 1       | 5.0V        | 5.0V   |
| 2       | GPIO21_EXT  | 3.3V   |
| 3       | GPIO22_EXT  | 3.3V   |
| 4       | GPIO23_EXT  | 3.3V   |
| 5       | GPIO24_EXT  | 3.3V   |
| 6       | GND         | GND    |

### Pi Fan - 4针JST-GH

| 引脚编号 | 信号名称       | 电压   |
| :------- | :------------- | :----- |
| 1        | GND            | GND    |
| 2        | 5.0V           | 5.0V   |
| 3        | 风扇转速检测   | 5.0V   |
| 4        | 风扇PWM控制*   | 5.0V   |

### Pi 控制台 - 6 针 JST-SH

| 针脚编号 | 信号名称    | 电压  |
| :------- | :---------- | :---- |
| 1        | 3.3V_RPI    | 3.3V  |
| 2        | CONSOLE_TXD0 | 3.3V  |
| 3        | CONSOLE_RXD0 | 3.3V  |
| 4        | NC           | 未连接 |
| 5        | NC           | 未连接 |
| 6        | GND          | GND   |

### Pi USB - 4针JST-GH (VBUS2_FLT)

| 引脚编号 | 信号名称     | 电压   |
| :------- | :----------- | :----- |
| 1        | VBUS2_FLT    | 5.0V   |
| 2        | USB2_EXT_N   | 3.3V   |
| 3        | USB2_EXT_P   | 3.3V   |
| 4        | GND          | GND    |

### Pi USB - 4针JST-GH (VBUS4_FLT)

| 引脚编号 | 信号名称    | 电压   |
| :------- | :---------- | :----- |
| 1        | VBUS4_FLT   | 5.0V   |
| 2        | USB4_EXT_N  | 3.3V   |
| 3        | USB4_EXT_P  | 3.3V   |
| 4        | GND         | GND    |

### CAM0 - 22针

0.5mm FFC 0545482271

| 引脚编号 | 信号名称     | 电压   |
| :------- | :----------- | :----- |
| 1        | GND          | GND    |
| 2        | CAM0_D0_N    | 1.2V   |
| 3        | CAM0_D0_P    | 1.2V   |
| 4        | GND          | GND    |
| 5        | CAM0_D1_N    | 1.2V   |
| 6        | CAM0_D1_P    | 1.2V   |
| 7        | GND          | GND    |
| 8        | CAM0_C_N     | 1.2V   |
| 9        | CAM0_C_P     | 1.2V   |
| 10       | GND          | GND    |
| 11       | NC           | NC     |
| 12       | NC           | NC     |
| 13       | GND          | GND    |
| 14       | NC           | NC     |
| 15       | NC           | NC     |
| 16       | GND          | GND    |
| 17       | CAM_GPIO     | 3.3V   |
| 18       | NC           | NC     |
| 19       | GND          | GND    |
| 20       | ID_SC        | 3.3V   |
| 21       | ID_SD        | 3.3V   |
| 22       | 3.3V_RPI     | 3.3V   |

### CAM1 - 22针0.5mm FFC 0545482271

| 针脚编号 | 信号名称 | 电压 |
| :--------- | :---------- | :------ |
| 1          | GND         | GND     |
| 2          | CAM1_D0_N   | 1.2V    |
| 3          | CAM1_D0_P   | 1.2V    |
| 4          | GND         | GND     |
| 5          | CAM1_D1_N   | 1.2V    |
| 6          | CAM1_D1_P   | 1.2V    |
| 7          | GND         | GND     |
| 8          | CAM1_C_N    | 1.2V    |
| 9          | CAM1_C_P    | 1.2V    |
| 10         | GND         | GND     |
| 11         | CAM1_D2_N   | 1.2V    |
| 12         | CAM1_D2_P   | 1.2V    |
| 13         | GND         | GND     |
| 14         | CAM1_D3_N   | 1.2V    |
| 15         | CAM1_D3_P   | 1.2V    |
| 16         | GND         | GND     |
| 17         | CAM_GPIO    | 3.3V    |
| 18         | NC          | NC      |
| 19         | GND         | GND     |
| 20         | SCL0        | 3.3V    |
| 21         | SDA0        | 3.3V    |
| 22         | 3.3V_RPI    | 3.3V    |

## 参见

- [ARK Pi6X Flow 文档](https://arkelectron.gitbook.io/ark-documentation/flight-controllers/ark-pi6x-flow) (ARK 文档)