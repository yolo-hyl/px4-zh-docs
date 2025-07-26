

# Holybro Pixhawk Jetson Baseboard

[Holybro Pixhawk Jetson Baseboard](https://holybro.com/products/pixhawk-jetson-baseboard) 将 Pixhawk 飞控和 NVIDIA Orin 系列计算单元整合为单一模块，极大简化了 PX4 与伴飞计算机的软硬件配置过程。

![Jetson 载板与 Pixhawk](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/hero_image.png)

该开发板提供 [Jetson Orin NX (16GB RAM)](https://holybro.com/products/nvidia-jetson-orin-nx-16g) 或 [Jetson Orin Nano (4GB RAM)](https://holybro.com/products/nvidia-jetson-orin-nx-16g?variant=44391410598077) 两种型号选择。可配合任意符合 Pixhawk Autopilot Bus (PAB) 规范的 Pixhawk 飞控使用，例如 Pixhawk 6 或 Pixhawk 6X。

本指南将引导完成以下操作流程：

- 硬件概览与配置
- Jetson 开发板烧写与 SSH 登录
- Pixhawk 上 PX4 固件烧写（及编译）
- Pixhawk 与 Jetson 的串口/以太网连接配置
- 通过串口/以太网接口的 MAVLink 设置与测试
- 通过串口/以太网接口的 ROS 2/XRCE-DDS 设置与测试

::: tip
您需要临时准备以下硬件设备以登录 Jetson 并获取其 IP 地址，之后即可通过 SSH 登录：

- 外接显示器
  如果您的显示器没有 mini HDMI 接口，还需要 [Mini HDMI 转 HDMI 转接器](https://a.co/d/6N815N9)（当显示器支持 HDMI 输入时）
- 网线
- 鼠标和键盘（开发板提供 Jetson 的 4 个 USB 接口，其中两个为 USB 3.0）

:::

## 购买

- [Holybro Pixhawk Jetson Baseboard](https://holybro.com/products/pixhawk-jetson-baseboard)

可以选择Pixhawk自动驾驶仪和Jetson计算机版本。
所有板载都配备WiFi模块、摄像头、电源模块、独立UBEC、电源分配板（PDB）。

## 规格

此信息来自 [Holybro Pixhawk-Jetson Baseboard 文档](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard)。

:::: tabs

::: tab 尺寸

[尺寸与重量](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard/dimension-and-weight) (Holybro)

- 尺寸

  - 126 x 80 x 45mm (含 Jetson Orin NX + 散热器/风扇 & 飞控模块)
  - 126 x 80 x 22.9mm (不含 Jetson 和飞控模块)

- 重量
  - 190g (含 Jetson、散热器、飞控、M.2 SSD、M.2 Wi-Fi 模块)

:::

::: tab Jetson 接口

- 2个千兆以太网端口

  - 通过以太网交换机(RTL8367S)连接至Jetson和飞控
  - 以太网交换机由与Pixhawk相同的电路供电
  - 8针 JST-GH
  - RJ45

- 2个 MIPI CSI 摄像头输入

  - 每个4通道
  - 22针 Raspberry Pi 摄像头FFC

- 2个 USB 3.0 主机端口

  - USB A
  - 5A 电流限制

- 2个 USB 2.0 主机端口

  - 5针 JST-GH
  - 0A 电流限制

- 用于编程/调试的 USB 2.0

  - USB-C

- 2个 Key M 2242/2280 接口用于NVMe SSD

  - PCIEx4

- 2个 Key E 2230 接口用于WiFi/BT

  - PCIEx2
  - USB
  - UART
  - I2S

- Mini HDMI 输出

- 4个 GPIO

  - 6针 JST-GH

- CAN端口

  - 连接到飞控的 CAN2 (4针 JST-GH)

- SPI端口

  - 7针 JST-GH

- I2C端口

  - 4针 JST-GH

- I2S端口

  - 7针 JST-GH

- 2个 UART端口

  - 1个用于调试
  - 1个连接到飞控的telem2

- 风扇电源端口

- IIM42652 IMU

:::

::: tab 自动驾驶仪接口

- Pixhawk 自动驾驶总线接口

  - 100针 Hirose DF40
  - 50针 Hirose DF40

- 冗余数字电源模块输入

  - 支持I2C电源监控
  - 2个6针 Molex CLIK-Mate

- 电源路径选择器

- 过压保护

- 电压规格

  - 最大输入电压: 6V
  - USB电源输入: 4.75~5.25V

- 完整GPS+安全开关端口

  - 10针 JST-GH

- 次级(GPS2)端口

  - 6针 JST-GH

- 2个 CAN端口

  - 4针 JST-GH

- 3个带流量控制的遥测端口

  - 2个6针 JST-GH
  - 1个连接到Jetson的UART1端口

- 16个PWM输出

  - 2个10针 JST-GH

- UART4 & I2C端口

  - 6针 JST-GH

- 2个千兆以太网端口

  - 通过以太网交换机(RTL8367S)连接至Jetson和飞控
  - 8针 JST-GH
  - RJ45

- AD & IO

  - 8针 JST-GH

- USB 2.0

  - USB-C
  - 4针 JST-GH

- DSM输入

  - 3针 JST-ZH 1.5mm间距

- RC输入

  - PPM/SBUS
  - 5针 JST-GH

- SPI端口

  - 外部传感器总线(SPI5)
  - 11针 JST-GH

- 2个调试端口

  - 1个用于FMU
  - 1个用于IO
  - 10针 JST-SH

:::

::: tab 电源(底板)

Pixhawk通过Jetson旁边的Power 1或Power 2端口供电。
Jetson的电源连接是同侧板上的XT30插头。

Jetson的电源电路与Pixhawk自动驾驶仪分离：

- 8V/3A 最小（取决于使用情况和外设）
- 电压范围: 7-21V (3S-4S)
- Jetson底板上的BEC支持7-21V (3S-4S)
  注意：对于4S以上应用可使用外部UBEC-12A

开发时建议使用以下有线电源适配器：

- [Jetson Orin电源适配器](https://holybro.com/products/power_adapter_for_jetson_orin)


完整的电源供应框图如下：

![Jetson 载板电源图](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/peripherals_block_diagram_1.png)

:::

::: tab 电源(UBEC-12A)

Holybro UBEC 12A (3-14S) BEC可用于更高功率应用(4S)。
这比底板内置BEC提供更大功率，并在BEC故障时提供冗余和更易更换。

电源规格：

- 输入电压: 3~14S (XT30)
- 输出电压: 6.0V/7.2V/8.0V/9.2V (建议为Jetson供电时使用7.2V)
- 输出电流
- 持续: 12A
- 峰值: 24A

尺寸

- 尺寸: 48x33.6x16.3 mm
- 重量: 47.8g

:::

::::

## 引脚分配

![Jetson 接口引脚](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/jetson_pinout.png)

::: details Pixhawk 接口

### 电源1（主）、电源2端口

(2.00mm Pitch CLIK-Mate)

| 针脚       | 信号              | 电压   |
| :-------- | :------------------ | :----- |
| 1 (红色)   | VDD5V_BRICK1/2(in)  | +5V    |
| 2 (黑色)   | VDD5V_BRICK1/2 (in) | +5V    |
| 3 (黑色)   | SCL1/2              | +3.3V  |
| 4 (黑色)   | SDA1/2              | +3.3V  |
| 5 (黑色)   | GND                 | GND    |
| 6 (黑色)   | GND                 | GND    |

### Tel1, Tel3 端口

| 引脚       | 信号         | 电压   |
| :-------- | :----------- | :----- |
| 1 (红色)   | 电源输出     | +5V    |
| 2 (黑色)   | 发送7/2(输出) | +3.3V  |
| 3 (黑色)   | 接收7/2(输入) | +3.3V  |
| 4 (黑色)   | 清空发送7/2(输入) | +3.3V |
| 5 (黑色)   | 请求发送7/2(输出) | +3.3V |
| 6 (黑色)   | 地           | GND    |

### CAN1, CAN2端口

| 引脚       | 信号       | 电压   |
| :-------- | :-------- | :------ |
| 1（红色）  | VCC（输出） | +5V    |
| 2（黑色）  | CANH1/2    | +3.3V  |
| 3（黑色）  | CANL1/2    | +3.3V  |
| 4（黑色）  | GND        | GND    |

### GPS1 接口

| 引脚        | 信号            | 电压 |
| :--------- | :---------------- | :------ |
| 1 (红色)    | VCC (输出)        | +5V     |
| 2 (黑色)    | TX1(输出)         | +3.3V   |
| 3 (黑色)    | RX1(输入)         | +3.3V   |
| 4 (黑色)    | SCL1              | +3.3V   |
| 5 (黑色)    | SDA1              | +3.3V   |
| 6 (黑色)    | SAFETY_SWITCH     | +3.3V   |
| 7 (黑色)    | SAFETY_SWITCH_LED | +3.3V   |
| 8 (黑色)    | VDD_3V3           | +3.3V   |
| 9 (黑色)    | BUZZER-           | 0\~5V   |
| 10 (黑色)   | GND               | GND     |

### GPS2端口

| 引脚       | 信号         | 电压   |
| :-------- | :---------- | :----- |
| 1 (红色)   | VCC (输出)  | +5V    |
| 2 (黑色)   | TX8(输出)   | +3.3V  |
| 3 (黑色)   | RX8(输入)   | +3.3V  |
| 4 (黑色)   | SCL2        | +3.3V  |
| 5 (黑色)   | SDA2        | +3.3V  |
| 6 (黑色)   | GND         | GND    |

### UART4与I2C端口

(在某些板上显示为UART\&I2C)

| 引脚       | 信号    | 电压 |
| :-------- | :-------- | :------ |
| 1 (红色)   | VCC (输出) | +5V     |
| 2 (黑色)   | TX4(输出)  | +3.3V   |
| 3 (黑色)   | RX4(输入)  | +3.3V   |
| 4 (黑色)   | SCL3      | +3.3V   |
| 5 (黑色)   | SDA3      | +3.3V   |
| 6 (黑色)   | NFC_GPIO  | +3.3V   |
| 7 (黑色)   | GND       | GND     |

### SPI端口

| 引脚       | 信号         | 电压  |
| :--------- | :----------- | :---- |
| 1 (红色)   | VCC (输出)   | +5V   |
| 2 (黑色)   | SPI6_SCK     | +3.3V |
| 3 (黑色)   | SPI6_MISO    | +3.3V |
| 4 (黑色)   | SPI6_MOSI    | +3.3V |
| 5 (黑色)   | SPI6_CS1     | +3.3V |
| 6 (黑色)   | SPI6_CS2     | +3.3V |
| 7 (黑色)   | SPIX_SYNC    | +3.3V |
| 8 (黑色)   | SPI6_DRDY1   | +3.3V |
| 9 (黑色)   | SPI6_DRDY2   | +3.3V |
| 10 (黑色)  | SPI6_nRESET  | +3.3V |
| 11 (黑色)  | GND          | GND   |

### FMU USB端口

| 引脚       | 信号    | 电压  |
| :-------- | :-------- | :------ |
| 1（红色）   | VBUS（输入） | +5V     |
| 2（黑色） | DM        | +3.3V   |
| 3（黑色） | DP        | +3.3V   |
| 4（黑色） | GND       | GND     |

### I2C端口

| 引脚       | 信号 | 电压 |
| :-------- | :----- | :------ |
| 1 (red)   | VCC    | +5V     |
| 2 (black) | SCL3   | +3.3V   |
| 3 (black) | SDA3   | +3.3V   |
| 4 (black) | GND    | GND     |

### ETH-P1 端口

| 引脚       | 信号     | 电压 |
| :-------- | :------- | :--- |
| 1 (红色)   | TX_D1+   | -    |
| 2 (黑色)   | TX_D1-   | -    |
| 3 (黑色)   | RX_D2+   | -    |
| 4 (黑色)   | RX_D2-   | -    |
| 5 (黑色)   | Bi_D3+   | -    |
| 6 (黑色)   | Bi_D3-   | -    |
| 7 (黑色)   | Bi_D4+   | -    |
| 8 (黑色)   | Bi_D4-   | -    |

### IO调试端口

(JST-SH 1mm Pitch)

| 引脚        | 信号            | 电压   |
| :--------- | :-------------- | :----- |
| 1（红色）    | IO_VDD_3V3(out) | +3.3V  |
| 2（黑色）    | IO_USART1_TX    | +3.3V  |
| 3（黑色）    | NC              | -      |
| 4（黑色）    | IO_SWD_IO       | +3.3V  |
| 5（黑色）    | IO_SWD_CK       | +3.3V  |
| 6（黑色）    | IO_SWO          | +3.3V  |
| 7（黑色）    | IO_SPARE_GPIO1  | +3.3V  |
| 8（黑色）    | IO_SPARE_GPIO2  | +3.3V  |
| 9（黑色）    | IO_nRST         | +3.3V  |
| 10（黑色）   | GND             | GND    |

### FMU 调试端口

（JST-SH 1mm间距）

| 引脚        | 信号             | 电压     |
| :--------- | :----------------- | :------ |
| 1（红色）    | FMU_VDD_3V3（输出）   | +3.3V   |
| 2（黑色）  | FMU_USART3_TX      | +3.3V   |
| 3（黑色）  | FMU_USART3_RX      | +3.3V   |
| 4（黑色）  | FMU_SWD_IO         | +3.3V   |
| 5（黑色）  | FMU_SWD_CK         | +3.3V   |
| 6（黑色）  | SPI6_SCK_EXTERNAL1 | +3.3V   |
| 7（黑色）  | NFC_GPIO           | +3.3V   |
| 8（黑色）  | PH11               | +3.3V   |
| 9（黑色）  | FMU_nRST           | +3.3V   |
| 10（黑色） | GND                | GND     |

### AD&IO端口

| 引脚       | 信号         | 电压   |
| :-------- | :------------- | :------ |
| 1 (红色)   | VCC（输出）      | +5V     |
| 2 (黑色)   | FMU_CAP1       | +3.3V   |
| 3 (黑色)   | FMU_BOOTLOADER | +3.3V   |
| 4 (黑色)   | FMU_RST_REQ    | +3.3V   |
| 5 (黑色)   | NARMED         | +3.3V   |
| 6 (黑色)   | ADC1_3V3       | +3.3V   |
| 7 (黑色)   | ADC1_6V6       | +6.6V   |
| 8 (黑色)   | GND            | GND     |

### DSM 无线电端口

(JST-ZH 1.5mm 针距)

| 引脚       | 信号               | 电压   |
| :--------- | :----------------- | :----- |
| 1 (黄色)   | VDD_3V3_SPEKTRUM   | +3.3V  |
| 2 (黑色)   | GND                | GND    |
| 3 (灰色)   | DSM/Spektrum 输入  | +3.3V  |

### RC输入端口

| 引脚       | 信号            | 电压 |
| :-------- | :---------------- | :------ |
| 1 (红色)   | VDD_5V \_RC (输出) | +5V     |
| 2 (黑色)   | SBUS/PPM 输入       | +3.3V   |
| 3 (黑色)   | RSSI_IN           | +3.3V   |
| 4 (黑色)   | 未连接(NC)        | -       |
| 5 (黑色)   | 地(GND)           | GND     |

### SBUS输出端口

| 引脚       | 信号       | 电压   |
| :-------- | :--------- | :----- |
| 1 (红色)  | 无连接     | -      |
| 2 (黑色)  | SBUS_OUT   | +3.3V  |
| 3 (黑色)  | GND        | GND    |

### FMU PWM OUT (AUX OUT)

| 引脚       | 信号        | 电压     |
| :--------- | :---------- | :------- |
| 1 (红色)   | VDD_SERVO   | 0\~16V  |
| 2 (黑色)   | FMU_CH1     | +3.3V   |
| 3 (黑色)   | FMU_CH2     | +3.3V   |
| 4 (黑色)   | FMU_CH3     | +3.3V   |
| 5 (黑色)   | FMU_CH4     | +3.3V   |
| 6 (黑色)   | FMU_CH5     | +3.3V   |
| 7 (黑色)   | FMU_CH6     | +3.3V   |
| 8 (黑色)   | FMU_CH7     | +3.3V   |
| 9 (黑色)   | FMU_CH8     | +3.3V   |
| 10 (黑色)  | GND         | GND     |

### IO PWM OUT（MAIN OUT）

| 引脚        | 信号        | 电压      |
| :--------- | :---------- | :-------- |
| 1（红色）   | VDD_SERVO   | 0\~16V    |
| 2（黑色）   | IO_CH1      | +3.3V     |
| 3（黑色）   | IO_CH2      | +3.3V     |
| 4（黑色）   | IO_CH3      | +3.3V     |
| 5（黑色）   | IO_CH4      | +3.3V     |
| 6（黑色）   | IO_CH5      | +3.3V     |
| 7（黑色）   | IO_CH6      | +3.3V     |
| 8（黑色）   | IO_CH7      | +3.3V     |
| 9（黑色）   | IO_CH8      | +3.3V     |
| 10（黑色）  | GND         | GND       |

:::

::: details Jetson Orin ports

### Orin USB2.0端口

| 引脚       | 信号            | 电压   |
| :-------- | :------------- | :------ |
| 1 (红色)   | USB电源总线（输出） | +5V     |
| 2 (黑色)   | DM             | +3.3V   |
| 3 (黑色)   | DP             | +3.3V   |
| 4 (黑色)   | 地线            | GND     |
| 5 (黑色)   | 屏蔽层          | GND     |

### Orin 调试

(JST-SH 1毫米间距)

| 引脚       | 信号             | 电压    |
| :-------- | :------------- | :------ |
| 1 (红色)   | VCC (out)      | +5V     |
| 2 (黑色)   | Orin_UART2_TXD | +3.3V   |
| 3 (黑色)   | Orin_UART2_RXD | +3.3V   |
| 4 (黑色)   | NC             | -       |
| 5 (黑色)   | NC             | -       |
| 6 (黑色)   | GND            | GND     |

### Orin I2C端口

| 引脚       | 信号           | 电压   |
| :-------- | :------------- | :----- |
| 1 (红色)   | VCC (输出)     | +5V    |
| 2 (黑色)   | Orin_I2C1_SCL  | +3.3V  |
| 3 (黑色)   | Orin_I2C1_SDA  | +3.3V  |
| 4 (黑色)   | GND            | GND    |

### Orin GPIO 端口

| 针脚       | 信号           | 电压   |
| :-------- | :----------- | :------ |
| 1 (红色)   | VCC          | +5V     |
| 2 (黑色)   | Orin_GPIO_07 | +3.3V   |
| 3 (黑色)   | Orin_GPIO_11 | +3.3V   |
| 4 (黑色)   | Orin_GPIO_12 | +3.3V   |
| 5 (黑色)   | Orin_GPIO_13 | +3.3V   |
| 6 (黑色)   | GND          | GND     |

### Orin Camera0 端口

相机串行接口（CSI）

| 引脚 | 信号            | 电压    |
| :-- | :---------------- | :------ |
| 1   | GND               | GND     |
| 2   | Orin_CSI1_D0_N    | +3.3V   |
| 3   | Orin_CSI1_D0_P    | +3.3V   |
| 4   | GND               | GND     |
| 5   | Orin_CSI1_D1_N    | +3.3V   |
| 6   | Orin_CSI1_D1_P    | +3.3V   |
| 7   | GND               | GND     |
| 8   | Orin_CSI1_CLK_N   | +3.3V   |
| 9   | Orin_CSI1_CLK_P   | +3.3V   |
| 10  | GND               | GND     |
| 11  | Orin_CSI0_D0_N    | +3.3V   |
| 12  | Orin_CSI0_D0_P    | +3.3V   |
| 13  | GND               | GND     |
| 14  | Orin_CSI0_D1_N    | +3.3V   |
| 15  | Orin_CSI0_D1_P    | +3.3V   |
| 16  | GND               | GND     |
| 17  | Orin_CAM0_PWDN    | +3.3V   |
| 18  | Orin_CAM0_MCLK    | +3.3V   |
| 19  | GND               | GND     |
| 20  | Orin_CAM0_I2C_SCL | +3.3V   |
| 21  | Orin_CAM0_I2C_SDA | +3.3V   |
| 22  | VDD               | +3.3V   |

### Orin 相机1端口

相机串行接口（CSI）

| 引脚 | 信号            | 电压 |
| :-- | :---------------- | :------ |
| 1   | 地               | 地     |
| 2   | Orin_CSI2_D0_N    | +3.3V   |
| 3   | Orin_CSI2_D0_P    | +3.3V   |
| 4   | 地               | 地     |
| 5   | Orin_CSI2_D1_N    | +3.3V   |
| 6   | Orin_CSI2_D1_P    | +3.3V   |
| 7   | 地               | 地     |
| 8   | Orin_CSI2_CLK_N   | +3.3V   |
| 9   | Orin_CSI2_CLK_P   | +3.3V   |
| 10  | 地               | 地     |
| 11  | Orin_CSI3_D0_N    | +3.3V   |
| 12  | Orin_CSI3_D0_P    | +3.3V   |
| 13  | 地               | 地     |
| 14  | Orin_CSI3_D1_N    | +3.3V   |
| 15  | Orin_CSI3_D1_P    | +3.3V   |
| 16  | 地               | 地     |
| 17  | Orin_CAM1_PWDN    | +3.3V   |
| 18  | Orin_CAM1_MCLK    | +3.3V   |
| 19  | 地               | 地     |
| 20  | Orin_CAM1_I2C_SCL | +3.3V   |
| 21  | Orin_CAM1_I2C_SDA | +3.3V   |
| 22  | VDD               | +3.3V   |

### Orin SPI端口

| 引脚       | 信号         | 电压   |
| :-------- | :------------- | :------ |
| 1 (red)   | VCC            | +5V     |
| 2 (black) | Orin_SPI0_SCK  | +3.3V   |
| 3 (black) | Orin_SPI0_MISO | +3.3V   |
| 4 (black) | Orin_SPI0_MOSI | +3.3V   |
| 5 (black) | Orin_SPI0_CS0  | +3.3V   |
| 6 (black) | Orin_SPI0_CS1  | +3.3V   |
| 7 (black) | GND            | GND     |

### Orin I2S 端口

| 引脚       | 信号          | 电压  |
| :-------- | :-------------- | :------ |
| 1 (红色)   | VCC             | +5V     |
| 2 (黑色)   | Orin_I2S0_SDOUT | +3.3V   |
| 3 (黑色)   | Orin_I2S0_SDIN  | +3.3V   |
| 4 (黑色)   | Orin_I2S0_LRCK  | +3.3V   |
| 5 (黑色)   | Orin_I2S0_SCLK  | +3.3V   |
| 6 (黑色)   | Orin_GPIO_09    | +3.3V   |
| 7 (黑色)   | GND             | GND     |

:::

## 硬件设置

底板同时提供了Pixhawk和Orin端口，如上文所示的[引脚分配](#引脚分配)。
Pixhawk端口符合Pixhawk连接器标准（针对标准覆盖的端口），这意味着该板可以按照多旋翼[多旋翼](../assembly/assembly_mc.md)、固定翼[fixed-wing](../assembly/assembly_fw.md)和[VTOL](../assembly/assembly_vtol.md)机体的通用组装说明，或对应Pixhawk指南（例如[Pixhawk 6X Quick Start](../assembly/quick_start_pixhawk6x.md)）进行外设连接（如GPS等）。

主要差异可能体现在电源设置（见下文）以及连接到Jetson的额外外设设置上。

### 外设

下图提供了关于外设应连接到哪些端口的进一步指导。

![Jetson Carrier Peripherals Diagram](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/peripherals_block_diagram_2.png)

### 电源接线

Pixhawk 和 Jetson 部分必须通过各自的电源端口分别供电。  
配套的电源模块支持 2S-12S 电池输入，并为 Pixhawk 部分提供稳压供电。  
其另一个输出通常连接到（配套的）电源分配板，然后通过该分配板为电机、舵机等供电，同时为 Jetson 供电（可直接供电或通过 UBEC 供电）。

Jetson 部分可通过 7V-21V 输入供电，对应 3S 或 4S 电池。  
如果使用电压高于 Jetson 允许范围的电池，可以通过 UBEC 提供较低的稳压供电，或使用独立电池为 Jetson 供电。

一些常见的接线配置如下所示。

#### 3S/4S电池

此配置演示如何使用3S/4S电池（输出电压低于21V）同时为Pixhawk和Jetson模块供电。

![电源布线 - 使用一个3S或4S电池](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/power1_one_battery_3s_4s.jpg)

#### 5S电池及更高电压（含UBEC）

此配置展示了如何使用外部UBEC（已提供）为Jetson提供合适电压，当使用高电压电池（>21V）时。
根据你的供电需求，也可以使用此UBEC（或另一UBEC）为控制面和其他舵机驱动的硬件提供合适的供电。

![电源接线 - 大容量电池和UBEC](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/power2_one_big_battery_external_ubec.png)

#### 双电池（无Ubec）

此配置展示了如何通过单独电池为Jetson提供适当电压，而非如上文所示那样通过大电池进行电压调节。

![电源布线 - 双电池无Ubec](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/power3_two_battery_no_ubec.jpg)

#### 使用电源适配器

在机体开发和测试过程中，建议通过外部电源为Jetson供电，如图所示。
Pixhawk部分可以通过USB-C电源或电池供电（如本图所示）。

![电源接线 - 电池和电源适配器](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/power4_battery_and_power_adapter.png)

## 烧录Jetson板

::: info
此Jetson配置已通过Nvidia Jetpack 6.0（Ubuntu 22.04基础版）和ROS 2 Humble测试，这些版本目前由PX4-Autopilot社区支持。
开发计算机同样运行Ubuntu 22.04。
:::

当Jetson板处于恢复模式时，可以通过开发计算机使用[Nvidia SDK Manager](https://docs.nvidia.com/sdk-manager/download-run-sdkm/index.html#download-sdk-manager)烧录Jetson伴飞计算机。
Jetson板进入恢复模式的方式有多种，但在此板上最佳方式是使用为此目的提供的小型滑动开关。
您还需要通过下图所示的特定USB-C端口将开发计算机连接到主板。

![USB端口和引导加载程序开关](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/flashing_usb.png)

::: tip
该USB端口仅用于烧录Jetson，不能用作调试接口。
:::

使用在线或离线安装程序下载[Nvidia SDK Manager](https://docs.nvidia.com/sdk-manager/download-run-sdkm/index.html#download-sdk-manager)（您需要拥有Nvidia账户才能安装和使用SDKManager）。

启动SDKManager后，您将看到类似下图的界面（如果板已通过恢复模式连接到主机计算机）：

![SDK Manager初始化界面](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/nvidia_sdkmanager_1.png)

下一个界面允许您选择要安装的组件。
如图所示选择_Jetson Linux_目标组件（无需选择其他组件，因为烧录后板将断开连接）。

![SDK Manager安装组件页面](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/nvidia_sdkmanager_2.png)

在下一个界面中确认所选设备：

- 在OEM配置中选择`Pre-config`（这将跳过重启后的Ubuntu首次设置界面）。
- 选择您偏好的用户名和密码（请记录下来）。
  这些将用作Jetpack的登录凭证。
- 选择`NVMe`作为存储设备，因为该板具有独立的SSD存储。

![SDK Manager安装存储和OEM配置页面](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/nvidia_sdkmanager_3.png)

按**Flash**开始安装镜像。
安装完成需要几分钟时间。

::: warning
安装过程中风扇会运行，因此请确保风扇未被阻挡。
烧录完成后Jetson将启动到初始登录界面。
:::

烧录完成后Jetson将重启到登录界面（除非已连接外部显示器，否则不会明显显示）。

请注意，当开关处于恢复模式位置时，恢复模式仅在烧录后的首次重启时跳过！
断开Jetson板的电源，将小型滑动开关从恢复模式切换回EMMC。
这确保了未来Jetson将启动到固件而非恢复模式。

## Jetson 网络与 SSH 登录

接下来我们确认 Jetson 的 WiFi 网络是否正常工作，查找其 IP 地址，并通过该 IP 地址进行 SSH 登录。

下图展示了如何将 Jetson 载板与外部键盘、显示器和鼠标连接。  
此步骤是必要的，以便我们配置网络连接。  
一旦建立了网络连接，我们就可以通过 SSH 连接到 Jetson，而无需使用外部显示器和键盘（如果需要的话）。

![首次网络设置连接图及电源](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/network_diagram.png)

将外部显示器连接到板载（Jetson）的 HDMI 接口，通过备用 USB 接口连接键盘和鼠标，并通过 XT-30 输入为 Jetson 供电。  
单独使用 USB-C（位于飞控侧）或 Jetson 旁边的 `Power1`/`Power2` 接口为 Pixhawk 供电。

::: tip
Pixhawk 也需要供电，因为板载以太网交换机不通过 XT30 接口供电。
:::

外部显示器应显示登录界面。  
输入使用 SDKManager 为 Jetson 刷写固件时设置的 _用户名_ 和 _密码_。

接下来打开终端以获取 Jetson 的 IP 地址。  
稍后可以使用该地址通过 SSH 连接。  
输入以下命令：

```sh
ip addr show
```

输出结果应类似于以下内容，显示（在此示例中）`wlan0`（WiFi）连接的 IP 地址为 `192.168.1.190`。  
注意 _eth0_ 在此步骤可能与本输出不同，因为我们尚未配置它，后续步骤将定义其设置：

```sh
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: l4tbr0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 3e:c7:e7:79:f3:87 brd ff:ff:ff:ff:ff:ff
3: usb0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast master l4tbr0 state DOWN group default qlen 1000
    link/ether 4e:fe:60:5f:cd:19 brd ff:ff:ff:ff:ff:ff
4: usb1: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast master l4tbr0 state DOWN group default qlen 1000
    link/ether 4e:fe:60:5f:cd:1b brd ff:ff:ff:ff:ff:ff
5: can0: <NOARP,ECHO> mtu 16 qdisc noop state DOWN group default qlen 10
    link/can
6: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 48:b0:2d:d8:d9:6f brd ff:ff:ff:ff:ff:ff
    altname enP8p1s0
    inet 10.41.10.1/24 brd 10.41.10.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::25eb:4f5b:2eab:6468/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
7: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 28:d0:ea:89:35:78 brd ff:ff:ff:ff:ff:ff
    altname wlP1p1s0
    inet 192.168.1.190/24 brd 192.168.1.255 scope global dynamic noprefixroute wlan0
       valid_lft 85729sec preferred_lft 85729sec
    inet6 fd71:5f79:6b08:6e4f:9f84:2646:dc24:ce7b/64 scope global temporary dynamic
       valid_lft 1790sec preferred_lft 1790sec
    inet6 fd71:5f79:6b08:6e4f:ec50:8cb1:91be:7dd6/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 1790sec preferred_lft 1790sec
    inet6 fe80::8276:b752:2b4b:5977/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

::: tip 如果命令无法正常工作...
板载的 WiFi 卡可能是 _Intel 8265NGW AC 双频_ 或 _Realtek RTL8B22CE_。  
Intel 模块可能需要手动安装驱动，出现此情况时，应在终端中运行以下命令进行安装：

```sh
sudo apt-get install -y backport-iwlwifi-dkms
```

然后重新尝试获取 IP 地址。
:::

获取到 IP 地址后，打开开发机的终端，输入以下命令连接 Jetson：

```sh
ssh <用户名>@<Jetson的IP地址>
```

系统会提示输入密码，完成认证后即可获得 Jetson 的命令行访问权限。

## 在Jetson上的初始开发环境设置

登录到Jetson后安装以下开发依赖项（您在大多数开发任务中都需要这些）：

```sh
sudo apt update
sudo apt install build-essential cmake git genromfs kconfig-frontends libncurses5-dev flex bison libssl-dev
```

## 构建/烧录 Pixhawk

更新 PX4 的推荐方式是使用开发用电脑对 Pixhawk 板载部分进行操作。  
你可以通过 QGroundControl 安装预编译的二进制文件，或者先构建再上传自定义固件。

另外，你也可以从 Jetson 构建并部署 PX4 固件到 Pixhawk 板载部分。

### 从开发计算机构建/部署 PX4（推荐）

首先将您的开发计算机连接到 Pixhawk FMU USB-C 接口（如下突出显示）：

![连接开发计算机到 Pixhawk USB 接口](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/pixhawk_fmu_usb_c.png)

如果您想使用预编译固件，请启动 QGroundControl 并按照 [加载固件](../config/firmware.md) 中的说明部署最新版本的 PX4。  
之后您需要选择一个机体并完成对应机体类型的所有常规配置。

如果您想构建并上传 _自定义固件_，请按照常规步骤操作：

- [设置开发环境（工具链）](../dev_setup/dev_env.md)  
- [构建 PX4 软件](../dev_setup/building_px4.md)  
- 使用 [命令行](../dev_setup/building_px4.md#uploading-firmware-flashing-the-board) 或 QGroundControl（如上）上传固件

### 在Jetson上构建PX4

您也可以在Jetson上构建和开发PX4代码。  
如果您有开发用计算机则无需执行此操作，但您仍可以这样操作。

首先打开终端并通过SSH连接到Jetson，输入以下命令克隆PX4源代码：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

::: tip
如果您不需要所有分支和标签，可以通过以下方式获取仓库的较小部分：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive --depth 1 --single-branch --no-tags
```

:::

然后输入以下命令安装Ubuntu构建工具链：

```sh
bash ./PX4-Autopilot/Tools/setup/ubuntu.sh --no-sim-tools
```

如果出现警告信息，可能需要运行以下命令将二进制目录路径添加到环境变量中：

```sh
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

::: tip
如果在运行`ubuntu.sh`时出现类似以下错误：

```sh
E: Unable to locate package g++-multilib
E: Couldn't find any package by regex 'g++-multilib'
```

您需要按照以下方式安装对应的gcc工具链，然后重新运行`ubuntu.sh`：

```sh
sudo apt install gcc-arm-none-eabi gdb-arm-none-eabi -y
```

:::

然后授予操作系统使用串口的权限：

```sh
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
```

通过为搭载板上的Pixhawk硬件构建PX4验证检查是否能构建PX4固件。  
假设您使用的是Pixhawk 6x，命令如下：

```sh
make px4_fmu-v6x_default
```

如果构建成功，可以按照下图方式将Pixhawk的USB-C接口连接到Jetson的USB接口（USB线缆随套件提供）。

![如何直接连接Jetson到Pixhawk](../../assets/companion_computer/holybro_pixhawk_jetson_baseboard/upload_px4_connection.png)

然后通过添加`upload`参数构建并上传固件到Pixhawk：

```sh
make px4_fmu-v6x_default upload
```

## 使用 Netplan 配置以太网

Holybro Jetson Pixhawk 载板在 Pixhawk 和 Jetson 之间内置了网络交换机，可用于 MAVLink 或 ROS 2 通信（或其他协议）。尽管无需连接任何外部网线，但仍需要配置以太网连接，使 Jetson 和 PX4 处于同一子网。

PX4 的默认 IP 地址为 `10.41.10.2`（PX4 v1.15 及更早版本使用 `192.168.0.3`），而 Jetson 通常配置在不同子网的默认 IP 地址上。为了使两块板卡通信，它们必须处于同一子网。

::: tip
这些说明大致对应 [PX4 以太网设置](../advanced_config/ethernet_setup.md)（以及 [Holybro Pixhawk RPi CM4 底板](../companion_computer/holybro_pixhawk_rpi_cm4_baseboard.md)）。
:::

接下来我们修改 Jetson 的 IP 地址以与 Pixhawk 处于同一网络：

1. 确保已安装 `netplan`。
   可通过运行以下命令检查：

   ```sh
   netplan -h
   ```

   如果未安装，请使用以下命令安装：

   ```sh
   sudo apt update
   sudo apt install netplan.io
   ```

2. 检查 `system_networkd` 是否正在运行：

   ```sh
   sudo systemctl status systemd-networkd
   ```

   如果处于活动状态，应看到如下类似输出：

   ```sh
   ● systemd-networkd.service - Network Configuration
        Loaded: loaded (/lib/systemd/system/systemd-networkd.service; enabled; vendor preset: enabled)
        Active: active (running) since Wed 2024-09-11 23:32:44 EDT; 23min ago
   TriggeredBy: ● systemd-networkd.socket
          Docs: man:systemd-networkd.service(8)
      Main PID: 2452 (systemd-network)
        Status: "Processing requests..."
         Tasks: 1 (limit: 18457)
        Memory: 2.7M
           CPU: 157ms
        CGroup: /system.slice/systemd-networkd.service
                └─2452 /lib/systemd/systemd-networkd

   Sep 11 23:32:44 ubuntu systemd-networkd[2452]: lo: Gained carrier
   Sep 11 23:32:44 ubuntu systemd-networkd[2452]: wlan0: Gained IPv6LL
   Sep 11 23:32:44 ubuntu systemd-networkd[2452]: eth0: Gained IPv6LL
   Sep 11 23:32:44 ubuntu systemd-networkd[2452]: Enumeration completed
   Sep 11 23:32:44 ubuntu systemd[1]: Started Network Configuration.
   Sep 11 23:32:44 ubuntu systemd-networkd[2452]: wlan0: Connected WiFi access point: Verizon_7YLWWD (78:67:0e:ea:a6:0>
   Sep 11 23:34:16 ubuntu systemd-networkd[2452]: eth0: Re-configuring with /run/systemd/network/10-netplan-eth0.netwo>
   Sep 11 23:34:16 ubuntu systemd-networkd[2452]: eth0: DHCPv6 lease lost
   Sep 11 23:34:16 ubuntu systemd-networkd[2452]: eth0: Re-configuring with /run/systemd/network/10-netplan-eth0.netwo>
   Sep 11 23:34:16 ubuntu systemd-networkd[2452]: eth0: DHCPv6 lease lost
   ```

   如果 `system_networkd` 未运行，可使用以下命令启用：

   ```sh
   sudo systemctl start systemd-networkd
   sudo systemctl enable systemd-networkd
   ```

3. 打开 Netplan 配置文件（以便为 Jetson 配置静态 IP）。

   Netplan 配置文件通常位于 `/etc/netplan/` 目录中，文件名类似 `01-netcfg.yaml`（名称可能因系统而异）。
   以下使用 `nano` 打开文件，但您也可以使用其他文本编辑器：

   ```sh
   sudo nano /etc/netplan/01-netcfg.yaml
   ```

4. 通过覆盖文件内容为以下信息并保存来修改 YAML 配置：

   ```sh
   network:
     version: 2
     renderer: networkd
     ethernets:
       eth0:
         dhcp4: no
         addresses:
           - 10.41.10.1/24
         routes:
           - to: 0.0.0.0/0
             via: 10.41.10.254
         nameservers:
           addresses:
             - 10.41.10.254
   ```

   此配置为 Jetson 的以太网接口分配静态 IP 地址 `10.41.10.1`。

5. 使用以下命令应用更改：

   ```sh
   sudo netplan apply
   ```

Pixhawk 的以太网地址默认设置为 `10.41.10.2`，与 Jetson 处于同一子网。我们可以通过在 Jetson 终端中 ping Pixhawk 来测试上述更改：

```sh
ping 10.41.10.2
```

如果成功，将看到如下输出：

```sh
PING 10.41.10.2 (10.41.10.2) 56(84) bytes of data.
64 bytes from 10.41.10.2: icmp_seq=1 ttl=64 time=0.215 ms
64 bytes from 10.41.10.2: icmp_seq=2 ttl=64 time=0.346 ms
64 bytes from 10.41.10.2: icmp_seq=3 ttl=64 time=0.457 ms
64 bytes from 10.41.10.2: icmp_seq=4 ttl=64 time=0.415 ms
```

Jetson 和 Pixhawk 现在可以通过以太网通信。

## MAVLink 配置

MAVLink 是一种用于 Jetson 与 PX4 之间通信的协议（下一节将讨论 ROS 2 作为替代方案）。  
PX4 用户通常使用 [MAVSDK](../robotics/mavsdk.md) 来管理 MAVLink 通信，因为它提供了对 MAVLink 协议的简单跨平台和跨编程语言的抽象。  

默认情况下，内部串口和以太网连接均配置为使用 MAVLink。  

::: info  
详见 [MAVLink-Bridge](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard/mavlink-bridge)（Holybro 文档）以获取更多信息。  
:::

### 串口连接

Jetson 与 Pixhawk 通过串口线缆内部连接，连接方式为 Pixhawk 的 `TELEM2` 接口连接到 Jetson 的 `THS1` 接口。  
Pixhawk 的 `TELEM2` 接口默认配置为使用 [MAVLink Peripherals (GCS/OSD/Companion) > TELEM2](../peripherals/mavlink_peripherals.md#telem2) 协议，因此无需特别操作即可正常工作。

一个测试连接的简便方法是运行 Jetson 上的 [MAVSDK-Python](https://github.com/mavlink/MAVSDK-Python) 示例代码。

首先安装 MAVSDK-Python：

```sh
pip3 install mavsdk
```

然后克隆仓库：

```sh
git clone https://github.com/mavlink/MAVSDK-Python --recursive
```

修改 `MAVSDK-Python/examples/telemetry.py` 中的 [连接代码](https://github.com/mavlink/MAVSDK-Python/blob/707c48c01866cfddc0082217dba9f7fe27d59b27/examples/telemetry.py#L10) 从：

```py
await drone.connect(system_address="udp://:14540")
```

更改为通过串口连接：

```sh
await drone.connect(system_address="serial:///dev/ttyTHS1:921600")
```

然后在 Jetson 终端运行示例代码：

```sh
python ~/MAVSDK-Python/examples/telemetry.py
```

预期输出如下：

``` sh
In air: False
Battery: -1.0
In air: False
Battery: -1.0
In air: False
Battery: -1.0
```

### 以太网连接

Jetson 和 Pixhawk 通过内部的以太网交换机连接，如果你已按照 [使用 Netplan 配置以太网](#使用 Netplan 配置以太网) 中的说明进行设置，那么它们也会处于同一个以太网子网中。

由于 Pixhawk 的 `Ethernet` 接口默认配置为使用 MAVLink ([MAVLink 外设 (地面站/画中画/伴飞) > 以太网](../peripherals/mavlink_peripherals.md#ethernet))，因此无需进行其他操作即可使用 MAVLink。

操作步骤与上一节相同，不同之处在于应修改 `connect()` 行为：

```sh
await drone.connect(system_address="udp://:14550")
```

## ROS 2 配置

[ROS 2](../ros2/index.md) 是一个强大的机器人API，您可以在Jetson上运行它来控制运行在Pixhawk上的PX4。
相比MAVLink，它更难学习且接口仍在演变，但能提供与PX4更深层的集成和更低延迟的通信。

要使用ROS 2，我们首先需要在PX4上运行[XRCE-DDS中间件](../middleware/uxrce_dds.md)客户端，并在Jetson上运行代理。
具体操作取决于所使用的通信通道（以太网或串行接口）。

本节详细介绍了具体配置方法，并主要基于[ROS 2用户指南](../ros2/user_guide.md)。

### PX4 XRCE-DDS 客户端设置

uXRCE-DDS 客户端默认集成在 PX4 固件中，通过 XRCE-Agent 将预定义的一组 uORB 话题暴露给运行 ROS 2 的 DDS 网络。  
所暴露话题的集合在构建配置文件中定义。  

客户端与代理通信所使用的通道通过参数进行配置。  
您可以使用内部串口连接或内部以太网交换机。

#### XRCE-Client串口设置

如MAVLink部分所述，Pixhawk的`TELEM2`与Jetson的`THS1`之间存在内部串口连接，默认配置为通过MAVLink通信。  
你需要禁用MAVLink实例并启用UXRCE_DDS配置。

你可以通过[修改参数](../advanced_config/parameters.md)在QGroundControl参数编辑器中操作，或通过[MAVLink shell](../debug/mavlink_shell.md)使用`param set`命令。

::: tip
访问MAVLink shell有多种方式。最便捷的方式是通过Pixhawk USB-C将Pixhawk连接到开发计算机，并使用[QGroundControl MAVLink Console](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)。
:::

以下说明假设你在MAVLink shell中输入命令：

```sh
param set MAV_1_CONFIG 0  # 禁用TELEM2上的MAVLINK（以便用于XRCE-DDS）
param set UXRCE_DDS_CFG 102 # 将UXRCE_DDS_CFG设置为TELEM2
```

::: info
你也可以修改以下参数，但这需要更深入的DDS知识。

```sh
param set UXRCE_DDS_DOM_ID 0
param set UXRCE_DDS_PTCFG 0
param set UXRCE_DDS_SYNCC 0
param set UXRCE_DDS_SYNCT 1
```

:::

然后重启Pixhawk。

#### XRCE-客户端以太网设置

如MAVLink部分所述，Jetson和Pixhawk之间通过一个内部以太网交换机相连。我们已为这两块板卡配置了相同的子网。但需要在PX4的`Ethernet`端口上禁用MAVLink并启用`XRCE-DDS`。

您可以通过QGroundControl参数编辑器[修改参数](../advanced_config/parameters.md)，或使用[MAVLink shell](../debug/mavlink_shell.md)中的`param set`命令进行设置。在MAVLink shell中输入以下命令以更改值：

```sh
param set MAV_2_CONFIG 0  # Disable MAVLINK on Ethernet (so Ethernet can be used for XRCE-DDS)
param set UXRCE_DDS_CFG 1000 # Ethernet
param set UXRCE_DDS_PRT 8888  # Set port to 8888 (default)
param set UXRCE_DDS_AG_IP 170461697 # The int32 version of 10.41.10.1
```

我们通过`UXRCE_DDS_AG_IP`参数将Jetson的地址设置为170461697。请注意，这是`10.41.10.1`的INT32格式版本（关于版本转换的详细信息请参见[启动uXRCE-DDS客户端](../middleware/uxrce_dds.md#starting-the-client)）。

#### 检查 XRCE-DDS 客户端是否运行

::: tip
如果配置正确，客户端应在启动时自动运行！
:::

我们可以通过 MAVLink 壳层中的以下命令检查客户端是否在运行：

```sh
uxrce_dds_client status
```

正常的输出应显示：

``` sh
nsh> uxrce_dds_client status
INFO [uxrce_dds_client] Running, disconnected
INFO [uxrce_dds_client] Using transport: serial
INFO [uxrce_dds_client] timesync converged: false
uxrce_dds_client: cycle: 0 events, 0us elapsed, 0.00us avg, min 0us max 0us 0.000us rms
uxrce_dds_client: cycle interval: 0 events, 0.00us avg, min 0us max 0us 0.000us rms
```

另一种检查客户端是否在启动时运行的方法是获取来自 MAVLINK 壳层的 `dmesg` 输出。  
以下输出作为 `dmesg` 日志的一部分，会提到波特率和实例（对于 Holybro Pixhawk 6X 和 6X Pro，`/dev/tty/S4` 等同于 `TELEM2`）：

```sh
Starting UXRCE-DDS Client on /dev/ttyS4
INFO  [uxrce_dds_client] init serial /dev/ttyS4 @ 921600 baud
```

### XRCE-DDS Agent & ROS 2 设置

请按照ROS2用户指南中的说明在Jetson上[安装ROS 2](../ros2/user_guide.md#install-ros-2)。  
由于我们不使用模拟器，因此无需在Jetson上安装PX4，但你可能希望安装完整的桌面环境，以便使用更多ROS 2软件包进行进一步开发：

sudo apt install -y ros-humble-desktop-full -y

#### 启动 XRCE-DDS 代理

然后按照 ROS 2 用户指南中[Setup the Agent](../ros2/user_guide.md#setup-the-agent)部分的说明获取并构建 uXRCE-DDS _agent_。  

根据客户端是配置为串口连接还是以太网连接，您将使用不同的命令启动代理。假设客户端已按上述方式设置：  

- （串口连接）在 `/dev/ttyTHS1` 上启动代理：  

  ```sh
  sudo MicroXRCEAgent serial --dev /dev/ttyTHS1 -b 921600
  ```

- （以太网）在 UDP 端口 `8888` 上启动代理：  

  ```sh
  MicroXRCEAgent udp4 -p 8888
  ```

如果代理和客户端已连接且没有节点运行，您应该会在代理终端看到类似以下的输出：  

```sh
[1726117589.065585] info     | UDPv4AgentLinux.cpp | init                     | running...             | port: 8888
[1726117589.066415] info     | Root.cpp           | set_verbose_level        | logger setup           | verbose_level: 4
[1726117589.568522] info     | Root.cpp           | create_client            | create                 | client_key: 0x00000001, session_id: 0x81
[1726117589.569317] info     | SessionManager.hpp | establish_session        | session established    | client_key: 0x00000001, address: 10.41.10.2:49940
[1726117589.580924] info     | ProxyClient.cpp    | create_participant       | participant created    | client_key: 0x00000001, participant_id: 0x001(1)
[1726117589.581598] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x800(2), participant_id: 0x001(1)
[1726117589.581761] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x800(4), participant_id: 0x001(1)
[1726117589.584858] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x800(6), subscriber_id: 0x800(4)
[1726117589.585643] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x801(2), participant_id: 0x001(1)
[1726117589.585746] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x801(4), participant_id: 0x001(1)
[1726117589.586284] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x801(6), subscriber_id: 0x801(4)
[1726117589.587115] info     | ProxyClient.cpp    | create_topic             | topic created          | client_key: 0x00000001, topic_id: 0x802(2), participant_id: 0x001(1)
[1726117589.587187] info     | ProxyClient.cpp    | create_subscriber        | subscriber created     | client_key: 0x00000001, subscriber_id: 0x802(4), participant_id: 0x001(1)
[1726117589.587536] info     | ProxyClient.cpp    | create_datareader        | datareader created     | client_key: 0x00000001, datareader_id: 0x802(6), subscriber_id: 0x802(4)
```

#### 开机启动代理

要让 Jetson 每次重启时自动运行 XRCE-DDS 代理，可以创建一个守护进程服务来运行代理。

创建一个新的服务文件：

```sh
sudo nano /etc/systemd/system/microxrceagent.service
```

将以下内容粘贴进去：

```plain
[Unit]
Description=Micro XRCE Agent Service After=network.target
[Service]
14
Holybro carrier board
ExecStart=/usr/local/bin/MicroXRCEAgent udp4 -p 8888 Restart=always
User=root
Group=root
ExecStartPre=/bin/sleep 10
[Install]
WantedBy=multi-user.target
```

保存文件后在终端运行以下命令：

```sh
sudo systemctl daemon-reload
sudo systemctl enable microxrceagent.service
```

然后你可以重启 Jetson 板并检查代理是否在后台运行：

```sh
sudo systemctl status microxrceagent.service
```

如果服务正在运行，你应该会看到类似以下的输出：

``` sh
holybro@ubuntu:~$ sudo systemctl status microxrceagent.service
● microxrceagent.service - Micro XRCE Agent Service
   Loaded: loaded (/etc/systemd/system/microxrceagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 204-07-30 01:37:45 EDT; 1min 30s ago
 Main PID: 1616 (MicroXRCEAgent)
    Tasks: 42 (limit: 18457)
   Memory: 29.6M
   CPU: 1.356s
   CGroup: /system.slice/microxrceagent.service
           └─1616 /usr/local/bin/MicroXRCEAgent udp4 -p 8888

Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.094190] info    | ProxyClient.cpp   | create_datawriter       | datawriter created   | client_key: 0x00000001, datawriter_id: 0x0F6(5), publisher_id: 0x0F6(3)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.095277] info    | ProxyClient.cpp   | create_topic            | topic created        | client_key: 0x00000001, topic_id: 0x100(2), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.095358] info    | ProxyClient.cpp   | create_publisher        | publisher created    | client_key: 0x00000001, publisher_id: 0x100(3), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.095537] info    | ProxyClient.cpp   | create_datawriter       | datawriter created   | client_key: 0x00000001, datawriter_id: 0x105(5), publisher_id: 0x100(3)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.096029] info    | ProxyClient.cpp   | create_topic            | topic created        | client_key: 0x00000001, topic_id: 0x105(2), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.097022] info    | ProxyClient.cpp   | create_publisher        | publisher created    | client_key: 0x00000001, publisher_id: 0x105(3), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.097797] info    | ProxyClient.cpp   | create_datawriter       | datawriter created   | client_key: 0x00000001, datawriter_id: 0x10A(5), publisher_id: 0x10A(3)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.098088] info    | ProxyClient.cpp   | create_topic            | topic created        | client_key: 0x00000001, topic_id: 0x10A(2), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.098168] info    | ProxyClient.cpp   | create_publisher        | publisher created    | client_key: 0x00000001, publisher_id: 0x10A(3), participant_id: 0x001(1)
Jul 30 01:37:52 ubuntu MicroXRCEAgent[1616]: [1722317872.098486] info    | ProxyClient.cpp   | create_datawriter       | datawriter created   | client_key: 0x00000001, datawriter_id: 0x10A(5), publisher_id: 0x10A(3)
```

你现在可以启动 ROS2 节点并继续开发。

### ROS 2 传感器综合测试

您可以使用[构建ROS 2工作区](../ros2/user_guide.md#build-ros-2-workspace)（ROS2用户指南）中的`sensor_combined`示例来测试客户端和代理。

::: tip
[通过SSH使用VSCode](https://code.visualstudio.com/learn/develop-cloud/ssh-lab-machines) 可提升您的ROS 2代码开发与修改应用效率！
:::

在运行示例到此阶段后：

```sh
ros2 launch px4_ros_com sensor_combined_listener.launch.py
```

您将看到高频传感器消息作为输出：

``` sh
[sensor_combined_listener-1] 接收到传感器综合数据
[sensor_combined_listener-1] ====================================
[sensor_combined_listener-1] ts: 1722316316179649
[sensor_combined_listener-1] gyro_rad[0]: 0.0015163
[sensor_combined_listener-1] gyro_rad[1]: 0.00191962
[sensor_combined_listener-1] gyro_rad[2]: 0.00343043
[sensor_combined_listener-1] gyro_integral_dt: 4999
[sensor_combined_listener-1] accelerometer_timestamp_relative: 0
[sensor_combined_listener-1] accelerometer_m_s2[0]: -0.368978
[sensor_combined_listener-1] accelerometer_m_s2[1]: 1.43863
[sensor_combined_listener-1] accelerometer_m_s2[2]: -9.68139
[sensor_combined_listener-1] accelerometer_integral_dt: 4999
```

## 另请参阅

- [Holybro Jetson 载板文档](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard)
- [PX4 中间件文档](../middleware/uxrce_dds.md#starting-the-client)
- [PX4 ROS 2 用户指南](../ros2/user_guide.md)