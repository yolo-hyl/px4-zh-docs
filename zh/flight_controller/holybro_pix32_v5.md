

# Holybro Pix32 v5

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

[Pix32 v5](https://holybro.com/products/pix32-v5)<sup>&reg;</sup>是一款由Holybro<sup>&reg;</sup>设计和制造的高级自动驾驶飞行控制器。
它优化用于运行PX4固件，适用于学术和商业开发者。
它基于[Pixhawk-project](https://pixhawk.org/)的**FMUv5**开放硬件设计，并在[NuttX](https://nuttx.apache.org/)操作系统上运行PX4。
它可视为Pixhawk4的一个变体版本。

Pix32 v5专为需要高性能、灵活且可定制的飞行控制系统的飞行员设计。
它由独立的飞行控制器和载板（基板）组成，两者通过100针连接器连接。
这种设计允许用户选择Holybro制造的基板，或自行定制。

![Pix32 v5系列](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_family.jpg)

::: info
此飞行控制器是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 快速概览

- 主FMU处理器：STM32F765  
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM  
- IO处理器：STM32F100  
  - 32位Arm® Cortex®-M3，24MHz，8KB SRAM  
- 板载传感器：  
  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：BMI055 或 ICM20602  
  - 磁力计：IST8310  
  - 气压计：MS5611  
- GPS：u-blox Neo-M8N GPS/GLONASS接收器；集成磁力计IST8310  
- 接口：  
  - 8-16路PWM输出（8路来自IO，8路来自FMU）  
  - FMU上3路专用PWM/捕获输入  
  - 专用R/C输入用于CPPM  
  - 专用R/C输入用于Spektrum/DSM和S.Bus，带模拟/PWM RSSI输入  
  - 专用S.Bus舵机输出  
  - 5个通用串行端口  
    - 2个带全流量控制  
    - 1个带独立1.5A电流限制  
  - 3个I2C端口  
  - 4个SPI总线  
    - 1个内部高速SPI传感器总线，4个芯片选择和6个DRDY  
    - 1个内部低噪声SPI总线专用  
    - 气压计专用，2个芯片选择，无DRDY  
    - 1个内部SPI总线专用FRAM  
    - 支持传感器模块上的专用SPI校准EEPROM  
    - 1个外部SPI总线  
  - 最多2个CAN总线用于双CAN与串行电调  
    - 每个CAN总线有独立静默控制或电调RX-MUX控制  
    - 2个电池电压/电流的模拟输入  
    - 2个附加模拟输入  
- 电气系统：  
  - 电源模块输出：4.9~5.5V  
  - 最大输入电压：6V  
  - 最大电流检测：120A  
  - USB电源输入：4.75~5.25V  
  - 舵机电源输入：0~36V  
- 重量与尺寸：  
  - 尺寸：45x45x13.5mm  
  - 重量：33.0g  
- 环境数据、质量与可靠性：  
  - 工作温度：-40 ~ 85℃  
  - 存储温度：-40~85℃  
  - CE  
  - FCC  
  - RoHS合规（无铅）  

更多信息请参考[Pix32 V5技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5_technical_data_sheet_v1.1.pdf)。

## 购买渠道

从 [Holybro 官网](https://holybro.com/products/pix32-v5) 下单购买。

## 组装/设置

[Pix32 v5接线快速入门](../assembly/quick_start_holybro_pix32_v5.md)提供了如何组装必需/重要的外设的说明，包括GPS、Power Management Board等。

## 底板布局

![Pix32 v5 Image](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_base_boards_layout.jpg)

## 引脚分配

[pix32 v5 和 mini baseboard](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf)

## 尺寸

![Pix32 v5 Image](../../assets/flight_controller/holybro_pix32_v5/Dimensions_no_border.jpg)

## 电压规格

_Pix32 v5_ 可通过三个电源输入实现供电三重冗余。
三个电源总线分别为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源总线 **FMU PWM OUT** 和 **I/O PWM OUT**（0V 至 36V）不为飞控板供电（也不会被飞控板供电）。
必须为 **POWER1**、**POWER2** 或 **USB** 中的任意一个供电，否则飞控板将处于无电状态。
:::

**正常工作最大规格**

在以下条件下系统将按此顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大规格**

在以下条件下系统将不会消耗任何电源（处于非工作状态），但硬件保持完好。

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 不会受损）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 不会受损）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 不会受损）

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接合适的硬件时，它会被预先构建并由 _QGroundControl_ 自动安装。
:::

要[构建 PX4](../dev_setup/building_px4.md) 以用于此目标：

```
make holybro_pix32v5_default
```

## 调试端口

系统中的[串口控制台](../debug/system_console.md)和SWD接口运行在**FMU调试**端口

<!--而I/O控制台和SWD接口可通过**I/O调试**端口访问。-->

![FMU调试端口示意图](../../assets/flight_controller/holybro_pix32_v5/FMU_Debug_Port_Horizontal.jpg)

引脚定义采用标准的[Pixhawk调试迷你](../debug/swd_debug.md#pixhawk-debug-mini)接口，该接口定义在[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)文档中。

## 外设

- [数字空速传感器](../sensor/airspeed.md)
- [遥测无线电模块](../telemetry/index.md)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机架

任何可以通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或船只。
所有支持的配置集可在[机架参考](../airframes/airframe_reference.md)中查看。

## 额外信息

- [Pix32 v5 技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5_technical_data_sheet_v1.1.pdf)
- [Pix32 v5 引脚图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf)
- [Pix32 v5 底板原理图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5-BASE-Schematic_diagram.pdf)
- [Pix32 v5 Mini 底板原理图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5-Base-Mini-Board_Schematic_diagram.pdf)
- [FMUv5 参考设计引脚图](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)