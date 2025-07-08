# Holybro Pixhawk 6C

:::warning
PX4不生产此（或任何）自动驾驶仪。  
请联系[制造商](https://holybro.com/)获取硬件支持或合规性问题。  
:::

_Pixhawk 6C_<sup>&reg;</sup> 是Pixhawk®飞控系列的最新升级版本，由Holybro<sup>&reg;</sup>与PX4团队合作设计制造。

它配备高性能H7处理器，具有IMU冗余、温度控制的IMU板和成本效益设计，提供卓越的性能和可靠性。  
该产品符合Pixhawk [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

<img src="../../assets/flight_controller/pixhawk6c/pixhawk6c_hero_upright.png" width="250px" title="Pixhawk 6C直立图像" />

:::tip
该自动驾驶仪获得PX4维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。  
:::

## 简介

Pixhawk® 6C 是Pixhawk®飞控系列产品线的最新升级版本。

在Pixhawk® 6C内部，您将找到基于STMicroelectronics®的STM32H743处理器，配合Bosch®与InvenSense®的传感器技术，为您提供控制任何自主机体的灵活性与可靠性，适用于学术和商业应用。

Pixhawk 6C的H7微控制器搭载运行最高480 MHz的Arm® Cortex®-M7核心，配备2MB闪存和1MB RAM。凭借升级的处理能力，开发者可以更高效地进行开发工作，实现复杂算法和模型。

Pixhawk 6C内置高性能低噪声IMU，设计上兼顾成本效益并具备IMU冗余。振动隔离系统可过滤高频振动并降低噪声，确保测量精度，使机体实现更优的飞行性能。

Pixhawk® 6C特别适用于企业研发实验室、初创公司、学术界（研究机构、教授、学生）以及商业应用开发者。

**关键设计特点**

- 高性能STM32H743处理器，具备更强的计算能力和RAM
- 新型低成本设计，采用低剖面结构
- 新设计的集成式振动隔离系统，可过滤高频振动并降低噪声以确保测量精度
- IMU通过机载加热电阻器进行温度控制，确保IMU达到最佳工作温度# 技术规格

### **处理器与传感器**

- FMU处理器: STM32H743  
  - 32位Arm® Cortex®-M7，480MHz，2MB内存，1MB SRAM  
- IO处理器: STM32F103  
  - 32位Arm® Cortex®-M3，72MHz，64KB SRAM  
- 机载传感器  
  - 加速度计/陀螺仪: ICM-42688-P  
  - 加速度计/陀螺仪: BMI055  
  - 磁力计: IST8310  
  - 气压计: MS5611  

### **电气参数**

- 电压规格:  
  - 最大输入电压: 6V  
  - USB电源输入: 4.75~5.25V  
  - 舵机电源输入: 0~36V  
- 电流规格:  
  - TELEM1最大输出电流限制: 1.5A  
  - 其他所有端口总输出电流限制: 1.5A  

### **机械参数**

- 尺寸: 84.8 \* 44 \* 12.4 mm  
- 重量: 59.3g  

### **接口**

- 16路PWM舵机输出（8路来自IO，8路来自FMU）  
- 3个通用串口  
  - TELEM1 - 全流量控制，独立1.5A电流限制  
  - TELEM2 - 全流量控制  
  - TELEM3  
- 2个GPS端口  
  - GPS1 - 完整GPS端口（GPS+安全开关）  
  - GPS2 - 基础GPS端口  
- 1个I2C端口  
  - 支持传感器模块上的专用I2C校准EEPROM  
- 2条CAN总线  
  - CAN总线支持独立静音控制或电调RX-MUX控制  
- 2个调试端口:  
  - FMU调试  
  - I/O调试  
- 专用遥控输入（支持Spektrum/DSM、S.BUS、CPPM、模拟/PWM RSSI）  
- 专用S.BUS输出  
- 2个电源输入端口（模拟）  

- 其他特性:  
  - 工作与存储温度: -40 ~ 85°C## 购买渠道

从 [Holybro](https://holybro.com/products/pixhawk-6c) 购买。

## 装配/设置

[Pixhawk 6C Wiring Quick Start](../assembly/quick_start_pixhawk6c.md) 提供了如何装配所需/重要外设的说明，包括GPS、电源模块等。

## 引脚分配

- [Holybro Pixhawk 6C Pinout](https://docs.holybro.com/autopilot/pixhawk-6c/pixhawk-6c-pinout)

## 串口映射

| UART   | 设备       | 端口          |
| ------ | ---------- | ------------- |
| USART1 | /dev/ttyS0 | GPS1          |
| USART2 | /dev/ttyS1 | TELEM3        |
| USART3 | /dev/ttyS2 | Debug Console |
| UART5  | /dev/ttyS3 | TELEM2        |
| USART6 | /dev/ttyS4 | PX4IO         |
| UART7  | /dev/ttyS5 | TELEM1        |
| UART8  | /dev/ttyS6 | GPS2          |## 尺寸

- [Pixhawk 6C Dimensions](https://docs.holybro.com/autopilot/pixhawk-6c/dimensions)

## 电压规格

_Pixhawk 6C_ 如果提供三个电源输入可实现三重冗余供电。三个电源轨分别为：**POWER1**、**POWER2** 和 **USB**。

**正常操作最大电压**

在以下条件下，系统将按照以下顺序使用所有电源输入进行供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大电压**

在以下条件下，系统将不会消耗任何电力（即无法工作），但硬件不会损坏：

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 不会损坏）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 不会损坏）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 不会损坏）

**电压监测**

Pixhawk 6C 使用模拟电源模块。

Holybro 提供多种模拟 [电源模块](../power_module/index.md) 以满足不同需求：

- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)

## 构建固件

:::tip
大多数用户无需手动构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建PX4](../dev_setup/building_px4.md)为该目标：

```
make px4_fmu-v6c_default
```

<a id="debug_port"></a>## 调试端口

[PX4 系统控制台](../debug/system_console.md)和[SWD 接口](../debug/swd_debug.md)运行在 **FMU Debug** 端口。

引脚定义和连接器符合[Pixhawk Debug Full](../debug/swd_debug.md#pixhawk-debug-full) 接口规范，该规范定义于[Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 接口标准（JST SM10B 连接器）。

| 引脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1（红色）  | `Vtref`          | +3.3V |
| 2（黑色）  | 控制台 TX（输出） | +3.3V |
| 3（黑色）  | 控制台 RX（输入）  | +3.3V |
| 4（黑色）  | `SWDIO`          | +3.3V |
| 5（黑色）  | `SWCLK`          | +3.3V |
| 6（黑色）  | `SWO`            | +3.3V |
| 7（黑色）  | NFC GPIO         | +3.3V |
| 8（黑色）  | PH11             | +3.3V |
| 9（黑色）  | nRST             | +3.3V |
| 10（黑色） | `GND`            | GND   |

关于该端口使用的更多信息请参见：

- [SWD 调试端口](../debug/swd_debug.md)
- [PX4 系统控制台](../debug/system_console.md)（注意，FMU 控制台映射到 USART3）## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机型

任何可通过普通RC舵机或Futaba S-Bus舵机进行控制的多旋翼/固定翼飞机/地面车或船只。所有支持的配置可在 [机型参考](../airframes/airframe_reference.md) 中查看。

## 更多信息

- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6C 接线快速入门](../assembly/quick_start_pixhawk6c.md)
- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [FMUv6C 参考设计引脚图](https://docs.google.com/spreadsheets/d/1FcmWRKd6zjdz3-cnjEDYEmANKZOFzNSc/edit?usp=sharing&ouid=113251442407318461574&rtpof=true&sd=true).
- [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).