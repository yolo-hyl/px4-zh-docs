# Holybro Pix32 v5

:::warning
PX4 不生产此（或任何）自动驾驶仪。  
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Pix32 v5](https://holybro.com/products/pix32-v5)<sup>&reg;</sup> 是由 Holybro<sup>&reg;</sup> 设计和制造的先进自动驾驶仪飞行控制器。  
它针对 PX4 固件进行优化，适用于学术和商业开发者。  
该控制器基于 [Pixhawk-project](https://pixhawk.org/) **FMUv5** 开源硬件设计，并运行在 [NuttX](https://nuttx.apache.org/) 操作系统上。  
可以视作 Pixhawk4 的变体版本。

Pix32 v5 专为需要高功率、灵活且可定制飞行控制系统的飞手设计。  
它由独立的飞行控制器和载板（底板）组成，通过 100 针连接器连接。  
这种设计允许用户选择 Holybro 制造的底板，或自行定制。

![Pix32 v5 家族](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_family.jpg)

::: info
此飞控系统是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 快速概览

- 主 FMU 处理器：STM32F765  
  - 32 位 Arm® Cortex®-M7，216MHz，2MB 存储器，512KB RAM  
- IO 处理器：STM32F100  
  - 32 位 Arm® Cortex®-M3，24MHz，8KB SRAM  
- 板载传感器：  
  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：BMI055 或 ICM20602  
  - 磁力计：IST8310  
  - 气压计：MS5611  
- GPS：u-blox Neo-M8N GPS/GLONASS 接收器；集成磁力计 IST8310  
- 接口：  
  - 8-16 个 PWM 输出（8 个来自 IO，8 个来自 FMU）  
  - FMU 上 3 个专用 PWM/Capture 输入  
  - 专用 R/C 输入用于 CPPM  
  - 专用 R/C 输入用于 Spektrum / DSM 和 S.Bus，带模拟/PWM RSSI 输入  
  - 专用 S.Bus 舵机输出  
  - 5 个通用串口  
    - 2 个带完整流控制  
    - 1 个带单独 1.5A 电流限制  
  - 3 个 I2C 端口  
  - 4 个 SPI 总线  
    - 1 个内部高速 SPI 传感器总线，带 4 个芯片选择和 6 个 DRDY 信号  
    - 1 个内部低噪声 SPI 总线专用  
    - 气压计带 2 个芯片选择，无 DRDY 信号  
    - 1 个内部 SPI 总线专用 FRAM  
    - 支持传感器模块上的专用 SPI 校准 EEPROM  
    - 1 个外部 SPI 总线  
  - 最多 2 个 CAN 总线用于双 CAN 串口电调  
    - 每个 CAN 总线有独立静默控制或电调 RX-MUX 控制  
    - 电压/电流模拟输入，支持 2 节电池  
    - 2 个额外的模拟输入  
- 电气系统：  
  - 电源模块输出：4.9~5.5V  
  - 最大输入电压：6V  
  - 最大电流检测：120A  
  - USB 电源输入：4.75~5.25V  
  - 舵机供电输入：0~36V  
- 重量与尺寸：  
  - 尺寸：45x45x13.5mm  
  - 重量：33.0g  
- 环境数据、质量和可靠性：  
  - 工作温度：-40 ~ 85°c  
  - 存储温度：-40~85℃  
  - CE  
  - FCC  
  - RoHS 合规（无铅）  

更多详细信息请参考 [Pix32 V5 技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5_technical_data_sheet_v1.1.pdf)。

## 购买渠道

从 [制造商](https://holybro.com/) 处购买。

## 固件构建

使用以下命令构建固件：  
`make holybro_pix32v5_default`

## 额外信息

- [Pix32 v5 技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5_technical_data_sheet_v1.1.pdf)  
- [Pix32 v5 引脚图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf)  
- [Pix32 v5 载板原理图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5-BASE-Schematic_diagram.pdf)  
- [Pix32 v5 Mini 载板原理图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5-Base-Mini-Board_Schematic_diagram.pdf)  
- [FMUv5 参考设计引脚图](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)