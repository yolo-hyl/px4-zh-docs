

# Holybro Pixhawk 6C Mini

:::warning
PX4 不生产此类（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pixhawk 6C Mini_<sup>&reg;</sup> 是 Pixhawk® 飞控家族的最新升级版本，由 Holybro<sup>&reg;</sup> 与 PX4 团队合作设计制造。

它搭载高性能 H7 处理器，配备 IMU 冗余、温度控制的 IMU 板及成本效益高的设计，提供卓越的性能和可靠性。
该产品符合 Pixhawk [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

![Pixhawk6c mini A&B Image](../../assets/flight_controller/pixhawk6c_mini/HB_6C_MINI-A_B.jpg)

:::tip
此自动驾驶仪由 PX4 维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 简介

Pixhawk® 6C Mini 是 Pixhawk® 飞控系列的最新升级版本。

Pixhawk® 6C Mini 内部搭载基于 STMicroelectronics® 的 STM32H743 处理器，并结合 Bosch® 与 InvenSense® 的传感器技术，为任何自主机体提供灵活性和可靠性，适用于学术和商业应用。

Pixhawk 6C Mini 的 H7 微控制器采用 Arm® Cortex®-M7 内核，最高运行频率可达 480 MHz，配备 2MB 闪存和 1MB RAM。凭借升级的处理性能，开发者可以更高效地完成开发工作，支持复杂算法和模型的实现。

Pixhawk 6C Mini 内置高性能、低噪声的惯性测量单元（IMU），在保证成本效益的同时实现 IMU 冗余。其集成的减震系统可过滤高频振动并减少噪声，确保测量精度，从而提升机体整体飞行性能。

Pixhawk® 6C Mini 非常适合企业研发实验室、初创公司、学术机构（科研人员、教授、学生）以及商业应用场景。

**关键设计特点**

- 高性能 STM32H743 处理器，具备更强的计算能力和 RAM
- 新型成本效益设计，采用低轮廓外形结构
- 新型集成减震系统，可过滤高频振动并减少噪声以确保测量精度
- IMU 通过板载加热电阻进行温度控制，确保 IMU 最佳工作温度

## 技术规格

### **处理器与传感器**

- FMU处理器：STM32H743
  - 32位 Arm® Cortex®-M7，480MHz，2MB内存，1MB SRAM
- IO处理器：STM32F103
  - 32位 Arm® Cortex®-M3，72MHz，64KB SRAM
- 机载传感器
  - 加速度计/陀螺仪：ICM-42688-P
  - 加速度计/陀螺仪：BMI055
  - 磁力计：IST8310
  - 气压计：MS5611

### **电气数据**

- 电压等级：
  - 最大输入电压：6V
  - USB电源输入：4.75~5.25V
  - 舵机电源输入：0~36V
- 电流等级：
  - `TELEM1`` 最大输出电流限制器：1A
  - 所有其他端口合计输出电流限制器：1A

### **机械数据**

- 尺寸: 53.3 x 39 x 16.2 mm
- 重量: 39.2g

### **接口**

- 14- PWM舵机输出（8个来自IO，6个来自FMU）  
- 3个通用串口  
  - `TELEM1` - 支持完整流控，独立1A电流限制  
  - `TELEM2` - 支持完整流控  
- 2个GPS接口  
  - GPS1 - 完整GPS接口（GPS加安全开关）  
  - GPS2 - 基础GPS接口  
- 1个I2C接口  
  - 支持传感器模块上的专用I2C校准EEPROM  
- 2个CAN总线  
  - CAN总线支持独立静默控制或电调RX-MUX控制  
- 1个调试接口：  
  - FMU调试Mini  
- 专用遥控输入支持Spektrum/DSM和S.BUS、CPPM、模拟/PWM RSSI  
- 1个电源输入接口（模拟）  

- 其他特性：  
  - 工作及存储温度：-40 ~ 85°C

## 购买渠道

从 [Holybro](https://holybro.com/products/pixhawk-6c-mini) 订购。

## 组装/设置

Pixhawk 4 Mini 的接口与 Pixhawk 6C Mini 的接口非常相似。
请参考 [Pixhawk 4 Mini 接线快速入门](../assembly/quick_start_pixhawk4_mini.md)，其中提供了如何组装所需/重要外围设备（包括 GPS、电源模块等）的说明。

## 引脚分布

- [Holybro Pixhawk 6C Mini 端口引脚分布](https://docs.holybro.com/autopilot/pixhawk-6c-mini/pixhawk-6c-mini-ports)

## 串口映射

| 串口    | 设备         | QGC参数描述 | 飞控上的端口标签 |
| ------- | ------------ | ----------- | ---------------- |
| USART1  | /dev/ttyS0   | GPS1        | GPS1             |
| USART2  | /dev/ttyS1   | TELEM3      | N/A              |
| USART3  | /dev/ttyS2   | N/A         | FMU Debug        |
| UART5   | /dev/ttyS3   | TELEM2      | TELEM2           |
| USART6  | /dev/ttyS4   | PX4IO       | I/O PWM Out      |
| UART7   | /dev/ttyS5   | TELEM1      | TELEM1           |
| UART8   | /dev/ttyS6   | GPS2        | GPS2             |

<!-- See http://docs.px4.io/main/en/hardware/serial_port_mapping.html#serial-port-mapping -->

## 尺寸

![Pixhawk6c Mini A 尺寸](../../assets/flight_controller/pixhawk6c_mini/pixhawk_6c_mini_dimension.jpg)
![Pixhawk6c Mini B 尺寸](../../assets/flight_controller/pixhawk6c_mini/pixhawk_6c_mini_b_dimensions.jpg)

## 电压规格

_Pixhawk 6C Mini_ 如果接入两路电源可实现电源双重冗余。两路供电轨分别为：**POWER1** 和 **USB**。

**正常操作最大额定值**

在此条件下，系统将按以下顺序使用所有电源供电：

1. **POWER1** 输入 (4.9V 至 5.5V)
1. **USB** 输入 (4.75V 至 5.25V)

**绝对最大额定值**

在此条件下，系统将不会消耗任何电力（不会运行），但仍能保持完好：

1. **POWER1** 输入 (工作范围 4.1V 至 5.7V，0V 至 10V 不会损坏)
1. **USB** 输入 (工作范围 4.1V 至 5.7V，0V 至 6V 不会损坏)
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚 (0V 至 42V 不会损坏)

**电压监控**

Pixhawk 6C Mini 使用模拟电源模块。

Holybro 提供多种模拟 [power modules](../power_module/index.md) 以满足不同需求：

- [PM02 Power Module](../power_module/holybro_pm02.md)
- [PM06 Power Module](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 Power Module](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [PM08 Power Module](https://holybro.com/products/pm08-power-module-14s-200a)

## 构建固件

:::tip
大多数用户无需构建此固件！
它是预构建的，并在连接合适的硬件时由_QGroundControl_自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)针对此目标：

```
make px4_fmu-v6c_default
```

<a id="debug_port"></a>

## 调试端口

[PX4 System Console](../debug/system_console.md) 和 [SWD interface](../debug/swd_debug.md) 运行在 **FMU 调试** 端口上。

其引脚配置和连接器符合 [Pixhawk Debug Mini](../debug/swd_debug.md#pixhawk-debug-mini) 接口标准，该标准定义在 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 接口规范中（使用 JST SH 连接器）。

| 引脚    | 信号           | 电压   |
| ------- | -------------- | ----- |
| 1 (红色) | `Vtref`        | +3.3V |
| 2 (黑色) | 控制台 TX (输出) | +3.3V |
| 3 (黑色) | 控制台 RX (输入) | +3.3V |
| 4 (黑色) | `SWDIO`        | +3.3V |
| 5 (黑色) | `SWCLK`        | +3.3V |
| 6 (黑色) | `GND`          | GND   |

关于该端口的使用信息请参考：

- [SWD Debug Port](../debug/swd_debug.md)
- [PX4 System Console](../debug/system_console.md)（注意，FMU 控制台映射到 USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [数传电台模块](../telemetry/index.md):
  - [Holybro 数传电台](../telemetry/holybro_sik_radio.md)
  - [Holybro Microhard P900 电台](../telemetry/holybro_microhard_p900_radio.md)
  - [Holybro XBP9X 数传电台](../telemetry/holybro_xbp9x_radio.md)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机体结构

任何可使用普通遥控舵机或多路遥控器舵机（Futaba S-Bus舵机）控制的多旋翼/固定翼飞机/地面车辆或船只。  
完整的支持配置列表可参考 [机体结构参考](../airframes/airframe_reference.md)。

## 参见

- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [Pixhawk 4 Mini接线快速入门](../assembly/quick_start_pixhawk4_mini.md) (和 [Pixhawk 6C接线快速入门](../assembly/quick_start_pixhawk6c.md))
- [PM02电源模块](../power_module/holybro_pm02.md)
- [PM06电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [PM08电源模块](https://holybro.com/products/pm08-power-module-14s-200a)
- [FMUv6C参考设计引脚图](https://docs.google.com/spreadsheets/d/1FcmWRKd6zjdz3-cnjEDYEmANKZOFzNSc/edit?usp=sharing&ouid=113251442407318461574&rtpof=true&sd=true).
- [Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).