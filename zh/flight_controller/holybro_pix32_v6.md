# Holybro Pix32 v6

:::warning
PX4 不生产此类（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pix32 v6_<sup>&reg;</sup> 是对 pix32 v5 飞行控制器的最新升级版本。它采用模块化设计，是 Pixhawk 6C 的变种版本，使用相同的 FMUv6C Target。它由独立的飞行控制器和载板组成，通过 [100 针连接器](https://docs.holybro.com/autopilot/pix32-v6/download) 连接。该产品专为需要高功率、灵活且可定制的飞行控制系统的飞行员设计。

该产品配备高性能 H7 处理器，提供惯性测量单元（IMU）冗余、温度控制的 IMU 板，以及成本效益设计，带来卓越的性能和可靠性。其设计符合 [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

<img src="../../assets/flight_controller/pix32v6/pix32v6_fc_only.png" width="550px" title="pix32v6 立体图像" />

<!--
:::tip
此自动驾驶仪获得 PX4 维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::
-->

## 简介

在 Pix32 v6 内部，您将找到基于 STMicroelectronics® 的 STM32H743 处理器，结合来自 Bosch® 与 InvenSense® 的传感器技术，为您提供控制任何自主机体所需的高度灵活性与可靠性，适用于学术和商业应用。

Pix32 v6 的 H7 微控制器搭载 Arm® Cortex®-M7 核心，主频高达 480 MHz，配备 2MB 闪存和 1MB RAM。得益于升级的处理能力，开发者可以更高效地进行开发工作，实现复杂算法和模型。该控制器集成了高性能、低噪声的 IMUs，设计上兼顾成本效益和 IMU 冗余。通过振动隔离系统可过滤高频振动并降低噪声，确保测量精度，从而提升机体整体飞行性能。

这款飞行控制器非常适合需要经济且模块化设计的用户，支持使用定制底板。我们已将 [pix32 v6 base board schematics public](https://docs.holybro.com/autopilot/pix32-v6/download) 公开，您可以自行制作定制载板，也可以由我们协助完成。通过使用定制底板，可确保物理尺寸、引脚分配和电源需求与机体完美匹配，既保证所有必要连接，也避免冗余连接器带来的成本和体积负担。

**关键设计特点**

- 高性能 STM32H743 处理器，具有更强的计算能力和更大的内存
- 新型低成本设计，采用低剖面外形结构
- 集成振动隔离系统，过滤高频振动并降低噪声以确保测量精度
- IMUs 通过板载加热电阻进行温度控制，实现 IMUs 最佳工作温度

# 技术规格

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
  - USB电源输入: 4.75\~5.25V  
  - 舵机电压输入: 0\~36V  
- 电流规格:  
  - TELEM1最大输出电流限制器: 1.5A  
  - 所有其他端口总输出电流限制器: 1.5A  

### **机械参数**

- 飞控模块尺寸: 44.8 x 44.8 x 13.5  
- 飞控模块重量: 36g  

### **接口**

- 16个PWM舵机输出（8个来自IO，8个来自FMU）  
- 3个通用串口  
  - `TELEM1` - 完整流控制，独立1.5A电流限制  
  - `TELEM2` - 完整流控制  
  - `TELEM3`  
- 2个GPS端口  
  - `GPS1` - 完整GPS端口（含GPS和安全开关）  
  - `GPS2` - 基础GPS端口  
- 1个I2C端口  
  - 支持位于传感器模块上的专用I2C校准EEPROM  
- 2个CAN总线  
  - CAN总线支持独立静默控制或电调RX-MUX控制  
- 2个调试端口:  
  - FMU调试  
  - I/O调试  
- 专用遥控输入（支持Spektrum/DSM、S.BUS、CPPM、模拟/PWM RSSI）  
- 专用S.BUS输出  
- 2个电源输入端口（模拟）  

- 其他特性:  
  - 工作与存储温度: -40 ~ 85°c
  
## 购买渠道

从 [Holybro](https://holybro.com/collections/autopilot-flight-controllers/products/pix32-v6) 订购。

## 引脚分配

- [Holybro Pix32 v6 Baseboard Ports Pinout](https://docs.holybro.com/autopilot/pix32-v6/pix32-v6-baseboard-ports)
- [Holybro Pix32 v6 Baseboard Ports Pinout](https://docs.holybro.com/autopilot/pix32-v6/pix32-v6-mini-base-ports)

## 串口映射

| UART   | 设备         | 端口           |
| ------ | ------------ | -------------- |
| USART1 | /dev/ttyS0   | GPS1           |
| USART2 | /dev/ttyS1   | TELEM3         |
| USART3 | /dev/ttyS2   | Debug Console  |
| UART5  | /dev/ttyS3   | TELEM2         |
| USART6 | /dev/ttyS4   | PX4IO          |
| UART7  | /dev/ttyS5   | TELEM1         |
| UART8  | /dev/ttyS6   | GPS2           |

## 尺寸

- [Pix32v6尺寸](https://docs.holybro.com/autopilot/pix32-v6/dimensions)

## 电压规格

_Pix32 v6_ 如果提供三个电源输入，可通过三重冗余设计实现供电冗余。三个电源通道分别为：**USB**、**POWER1**、**POWER2**（Pix32 v6 Mini-Baseboard 不适用）。

**正常运行最大规格**

在以下条件下，系统将按此顺序使用所有电源输入：

1. **POWER1** 和 **POWER2** 输入（4.9V 到 5.5V）
1. **USB** 输入（4.75V 到 5.25V）

**绝对最大规格**

在以下条件下，系统不会消耗任何电力（即无法运行），但硬件不会受损：

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 到 5.7V，0V 到 10V 无损坏）
1. **USB** 输入（工作范围 4.1V 到 5.7V，0V 到 6V 无损坏）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 到 42V 无损坏）

**电压监控**

Pix32 v6 使用模拟电源模块。

Holybro 针对不同需求提供了多种模拟[电源模块](../power_module/index.md)：

- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动安装预构建的固件。
:::

要[构建 PX4](../dev_setup/building_px4.md)用于此目标：

```
make px4_fmu-v6c_default
```

<a id="debug_port"></a>

## 调试端口

[PX4系统控制台](../debug/system_console.md) 和 [SWD接口](../debug/swd_debug.md) 运行在 **FMU调试** 端口上。

引脚定义和连接器符合 [Pixhawk调试完整接口](../debug/swd_debug.md#pixhawk-debug-full) 标准，该标准定义于 [Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) (JST SM10B连接器) 中。

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

有关此端口使用的信息请参见：

- [SWD调试端口](../debug/swd_debug.md)
- [PX4系统控制台](../debug/system_console.md)（注意：FMU控制台映射到 USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机体

任何可通过普通遥控舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或水上船。
所有支持的配置可以在[Airframes Reference](../airframes/airframe_reference.md)中查看。

## 更多信息

- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [参考：Pixhawk 6C 线路快速入门](../assembly/quick_start_pixhawk6c.md)
- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [FMUv6C 参考设计引脚分配](https://docs.google.com/spreadsheets/d/1FcmWRKd6zjdz3-cnjEDYEmANKZOFzNSc/edit?usp=sharing&ouid=113251442407318461574&rtpof=true&sd=true).
- [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).