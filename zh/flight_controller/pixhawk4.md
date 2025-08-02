

# Holybro Pixhawk 4

:::warning
PX4不生产此（或任何）自动驾驶仪。有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pixhawk 4_<sup>&reg;</sup> 是一款先进的自动驾驶仪，由Holybro<sup>&reg;</sup>与PX4团队合作设计制造。  
该设备针对运行PX4 v1.7及以上版本进行了优化，适用于学术和商业开发者。  
它基于[Pixhawk项目](https://pixhawk.org/)**FMUv5** 开源硬件设计，并在[NuttX](https://nuttx.apache.org/) 操作系统上运行PX4。

<img src="../../assets/flight_controller/pixhawk4/pixhawk4_hero_upright.jpg" width="200px" title="Pixhawk4 竖直视图" /> <img src="../../assets/flight_controller/pixhawk4/pixhawk4_logo_view.jpg" width="420px" title="Pixhawk4 图像" />

:::tip
该自动驾驶仪由PX4维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 快速概览

- 主FMU处理器：STM32F765  
  - 32位 Arm® Cortex®-M7，216MHz，2MB存储，512KB RAM  
- IO处理器：STM32F100  
  - 32位 Arm® Cortex®-M3，24MHz，8KB SRAM  
- 机载传感器：  
  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：BMI055 或 ICM20602  
  - 磁力计：IST8310  
  - 气压计：MS5611  
- GPS：u-blox Neo-M8N GPS/GLONASS 接收器；集成磁力计 IST8310  
- 接口：  
  - 8-16个PWM输出（8个来自IO，8个来自FMU）  
  - 3个专用PWM/捕获输入（FMU）  
  - 专用R/C输入用于CPPM  
  - 专用R/C输入用于Spektrum/DSM和S.Bus，带模拟/PWM RSSI输入  
  - 专用S.Bus舵机输出  
  - 5个通用串口  
  - 3个I2C接口  
  - 4个SPI总线  
  - 最多2条CAN总线用于双CAN和串口电调  
  - 电池电压/电流的模拟输入（2组电池）  
- 电源系统：  
  - 电源模块输出：4.9~5.5V  
  - USB电源输入：4.75~5.25V  
  - 舵机供电输入：0~36V  
- 重量与尺寸：  
  - 重量：15.8g  
  - 尺寸：44x84x12mm  
- 其他特性：  
  - 工作温度：-40 ~ 85°c  

更多信息请参阅[Pixhawk 4 技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4/pixhawk4_technical_data_sheet.pdf)。

## 购买渠道

从 [Holybro](https://holybro.com/products/pixhawk-4) 购买。

## 接口

![Pixhawk 4 接口](../../assets/flight_controller/pixhawk4/pixhawk4-connectors.jpg)

:::warning
**DSM/SBUS RC** 和 **PPM RC** 端口仅用于接收机连接。
这些端口是带电的！切勿连接舵机、电源或电池（或连接到任何接收机）。
:::

## 引脚分配

从[此处](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Pixhawk4-Pinouts.pdf)下载 _Pixhawk 4_ 引脚分配。

::: info
连接器的针脚分配是从左到右（即Pin 1是左侧最外侧的针脚）。
例外的是[调试端口](#debug_port)（Pin 1是右侧最外侧的，如下所示）。
:::

## 串口映射

| UART   | Device     | Port                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | GPS                   |
| USART2 | /dev/ttyS1 | TELEM1 (流控制)       |
| USART3 | /dev/ttyS2 | TELEM2 (流控制)       |
| UART4  | /dev/ttyS3 | TELEM4                |
| USART6 | /dev/ttyS4 | RC SBUS               |
| UART7  | /dev/ttyS5 | 调试控制台            |
| UART8  | /dev/ttyS6 | PX4IO                 |

## 尺寸

![Pixhawk 4 尺寸](../../assets/flight_controller/pixhawk4/pixhawk4_dimensions.jpg)

## 电压规格

_Pixhawk 4_ 可在提供三个电源时实现电源三重冗余。三个电源轨道为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源轨道 **FMU PWM OUT** 和 **I/O PWM OUT**（0V 至 36V）不会为飞控板供电（也不会被其供电）。
必须为 **POWER1**、**POWER2** 或 **USB** 中的至少一个供电，否则飞控板将无电。
:::

**正常运行最大规格**

在以下条件下，系统将按此顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大规格**

在以下条件下，系统不会消耗任何电力（不会运行），但硬件保持完好。

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 无损）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 无损）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 无损）

## 组装/设置

[Pixhawk 4 Wiring Quick Start](../assembly/quick_start_pixhawk4.md) 提供了关于如何组装所需/重要外设（包括GPS、Power Management Board等）的说明。

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建 PX4](../dev_setup/building_px4.md) 用于此目标：

```
make px4_fmu-v5_default
```

<a id="debug_port"></a>

## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)运行在 **FMU调试**端口，而I/O控制台和SWD接口可通过 **I/O调试**端口访问。
要访问这些端口，用户必须拆除 _Pixhawk 4_ 外壳。

![Pixhawk 4 调试端口](../../assets/flight_controller/pixhawk4/pixhawk4_debug_port.jpg)

引脚分配使用标准的 [Pixhawk调试连接器引脚分配](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。
布线信息请参见：

- [系统控制台 > Pixhawk调试端口](../debug/system_console.md#pixhawk_debug_port)

## 外设

- [数字空速传感器](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html)  
- [遥测无线电模块](../telemetry/index.md)  
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机型

任何可使用普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/车或船。完整支持的配置列表可参考 [机型参考](../airframes/airframe_reference.md)。

## 更多信息

- [Pixhawk 4 技术数据表](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4/pixhawk4_technical_data_sheet.pdf)
- [FMUv5 参考设计引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165).
- [Pixhawk 4 接线快速入门](../assembly/quick_start_pixhawk4.md)
- [Pixhawk 4 引脚分配](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Pixhawk4-Pinouts.pdf) (Holybro)
- [Pixhawk 4 快速入门指南](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Pixhawk4-quickstartguide.pdf) (Holybro)