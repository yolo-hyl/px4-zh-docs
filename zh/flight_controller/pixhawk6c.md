

# Holybro Pixhawk 6C

:::warning
PX4并未制造此（或任何）自动驾驶仪。
关于硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pixhawk 6C_<sup>&reg;</sup> 是Pixhawk®系列飞行控制器的最新升级产品，由Holybro<sup>&reg;</sup>与PX4团队联合设计制造。

该产品搭载高性能H7处理器，配备IMU冗余、温度控制IMU板和高性价比设计，提供卓越的性能和可靠性。
其符合Pixhawk [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

<img src="../../assets/flight_controller/pixhawk6c/pixhawk6c_hero_upright.png" width="250px" title="Pixhawk6c Upright Image" />

:::tip
此自动驾驶仪获得了PX4维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 引言

Pixhawk® 6C 是Pixhawk®飞控家族的最新升级版本。

Pixhawk® 6C 内部搭载了基于STMicroelectronics®的STM32H743芯片，配合来自Bosch®和InvenSense®的传感器技术，为各类自主机体控制提供了灵活可靠的基础，适用于学术研究和商业应用。

Pixhawk® 6C的H7微控制器采用最高480 MHz运行频率的Arm® Cortex®-M7内核，配备2MB闪存和1MB RAM。得益于升级的处理能力，开发者能够更高效地进行开发工作，支持复杂算法和模型的运行。

Pixhawk 6C 集成了高性能低噪声惯性测量单元（IMU），在保证成本效益的同时具备IMU冗余设计。其创新减震系统可过滤高频振动并降低噪声，确保测量数据的准确性，从而提升机体整体飞行性能。

Pixhawk® 6C 适用于企业研发实验室、初创公司、学术机构（科研人员、教授、学生）及商业应用领域。

**关键设计要点**

- 高性能STM32H743处理器，具备更强的计算能力和更大RAM
- 新型成本优化设计，采用紧凑型低轮廓结构
- 新设计的集成式减震系统可过滤高频振动并降低噪声，确保测量精度
- IMU通过板载加热电阻进行温度控制，实现最佳工作温度

# 技术规格

### **处理器与传感器**

- 主飞行控制器：STM32H743  
  - 32位 Arm® Cortex®-M7，480MHz，2MB内存，1MB SRAM  
- 输入/输出处理器：STM32F103  
  - 32位 Arm® Cortex®-M3，72MHz，64KB SRAM  
- 板载传感器  
  - 加速度计/陀螺仪：ICM-42688-P  
  - 加速度计/陀螺仪：BMI055  
  - 磁强计：IST8310  
  - 气压计：MS5611

### **电气数据**

- 电压规格：
  - 最大输入电压：6V
  - USB供电输入：4.75～5.25V
  - 舵机供电输入：0～36V
- 电流规格：
  - TELEM1最大输出电流限制器：1.5A
  - 其他所有端口合计输出电流限制器：1.5A

### **机械数据**

- 尺寸：84.8 \* 44 \* 12.4 毫米
- 重量：59.3克

### **接口**

- 16个PWM舵机输出（8个来自IO，8个来自FMU）
- 3个通用串口
  - TELEM1 - 完整流控，独立1.5A电流限制
  - TELEM2 - 完整流控
  - TELEM3
- 2个GPS串口
  - GPS1 - 完整GPS串口（GPS加安全开关）
  - GPS2 - 基础GPS串口
- 1个I2C串口
  - 支持传感器模块上的专用I2C校准EEPROM
- 2个CAN总线
  - CAN总线具有独立静默控制或电调RX-MUX控制
- 2个调试端口：
  - FMU调试
  - I/O调试
- 专用R/C输入支持Spektrum/DSM和S.BUS、CPPM、模拟/PWM RSSI
- 专用S.BUS输出
- 2个电源输入端口（模拟）

- 其他特性：
  - 工作及存储温度：-40 ~ 85°C

## 购买渠道

从 [Holybro](https://holybro.com/products/pixhawk-6c) 订购。

## 组装/设置

[Pixhawk 6C 接线快速入门](../assembly/quick_start_pixhawk6c.md) 提供了组装必需/重要外围设备（如GPS、电源模块等）的说明。

## 引脚分配

- [Holybro Pixhawk 6C 引脚分配](https://docs.holybro.com/autopilot/pixhawk-6c/pixhawk-6c-pinout)

## 串口映射

| UART   | 设备         | 端口           |
| ------ | ------------ | -------------- |
| USART1 | /dev/ttyS0   | GPS1           |
| USART2 | /dev/ttyS1   | TELEM3         |
| USART3 | /dev/ttyS2   | 调试控制台     |
| UART5  | /dev/ttyS3   | TELEM2         |
| USART6 | /dev/ttyS4   | PX4IO          |
| UART7  | /dev/ttyS5   | TELEM1         |
| UART8  | /dev/ttyS6   | GPS2           |

## 尺寸

- [Pixhawk 6C 尺寸](https://docs.holybro.com/autopilot/pixhawk-6c/dimensions)

## 电压规格

_Pixhawk 6C_ 可在提供三个电源时实现电源三重冗余。三个电源总线分别为：**POWER1**、**POWER2** 和 **USB**。

**正常操作最大值**

在以下条件下，系统将按此顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大值**

在以下条件下，系统将不消耗任何电力（无法运行），但硬件保持完好：

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 无损坏）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 无损坏）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 无损坏）

**电压监控**

Pixhawk 6C 使用模拟电源模块。

Holybro 提供多种模拟 [电源模块](../power_module/index.md) 满足不同需求：

- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)

## 构建固件

:::tip
大多数用户不需要构建此固件！
它在连接合适硬件时由_QGroundControl_预先构建并自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以用于此目标：

```
make px4_fmu-v6c_default
```

<a id="debug_port"></a>

## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)运行在 **FMU调试** 端口上。

该端口的引脚分配和连接器符合[Pixhawk调试完整](../debug/swd_debug.md#pixhawk-debug-full)接口在[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)接口中定义的规范（JST SM10B连接器）。

| 引脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1 (red)  | `Vtref`          | +3.3V |
| 2 (blk)  | 控制台TX (OUT)  | +3.3V |
| 3 (blk)  | 控制台RX (IN)   | +3.3V |
| 4 (blk)  | `SWDIO`          | +3.3V |
| 5 (blk)  | `SWCLK`          | +3.3V |
| 6 (blk)  | `SWO`            | +3.3V |
| 7 (blk)  | NFC GPIO         | +3.3V |
| 8 (blk)  | PH11             | +3.3V |
| 9 (blk)  | nRST             | +3.3V |
| 10 (blk) | `GND`            | GND   |

关于使用该端口的更多信息请参见：

- [SWD调试端口](../debug/swd_debug.md)
- [PX4系统控制台](../debug/system_console.md)（注意，FMU控制台映射到USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台/机型

任何可通过普通遥控舵机或Futaba S-Bus舵机控制的多旋翼/固定翼飞机/地面车辆或船只。完整的支持配置列表请参见 [机型参考](../airframes/airframe_reference.md)。

## 更多信息

- [Holybro 官方文档](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6C 接线快速入门](../assembly/quick_start_pixhawk6c.md)
- [PM02 电源模块](../power_module/holybro_pm02.md)
- [PM06 电源模块](../power_module/holybro_pm06_pixhawk4mini_power_module.md)
- [PM07 电源模块](../power_module/holybro_pm07_pixhawk4_power_module.md)
- [FMUv6C 参考设计引脚图](https://docs.google.com/spreadsheets/d/1FcmWRKd6zjdz3-cnjEDYEmANKZOFzNSc/edit?usp=sharing&ouid=113251442407318461574&rtpof=true&sd=true).
- [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).