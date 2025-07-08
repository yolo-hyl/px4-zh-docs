# CUAV v5 (已停产)

<Badge type="info" text="已停产" />

:::warning
该飞控已[停产](../flight_controller/autopilot_experimental.md)且不再商业销售。
:::

:::warning
PX4 不生产任何自动驾驶仪（包括本产品）。
关于硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

_CUAV v5_<sup>&reg;</sup>（原 "Pixhack v5"）是CUAV<sup>&reg;</sup> 设计制造的先进自动驾驶仪。该板基于 [Pixhawk-project](https://pixhawk.org/) **FMUv5** 开源硬件设计。它在 [NuttX](https://nuttx.apache.org/) 操作系统上运行 PX4 固件，完全兼容 PX4 固件。主要面向学术和商业开发者。

![CUAV v5](../../assets/flight_controller/cuav_v5/pixhack_v5.jpg)

## 快速概览

- 主FMU处理器：STM32F765
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM
- IO处理器：STM32F100
  - 32位Arm® Cortex®-M3，24MHz，8KB SRAM
- 板载传感器：

  - 加速度计/陀螺仪：ICM-20689
  - 加速度计/陀螺仪：BMI055
  - 磁力计：IST8310
  - 气压计：MS5611

- 接口：
  - 8-14路PWM输出（6路来自IO，8路来自FMU）
  - 3路专用PWM/捕获输入（FMU）
  - 专用R/C输入（CPPM）
  - 专用R/C输入（PPM和S.Bus）
  - 模拟/PWM RSSI输入
  - S.Bus舵机输出
  - 5个通用串口
  - 4个I2C端口
  - 4个SPI总线
  - 2个CAN总线（带串口电调）
  - 2块电池电压/电流模拟输入
- 电源系统：
  - 供电：4.3~5.4V
  - USB输入：4.75~5.25V
  - 舵机供电输入：0~36V
- 重量与尺寸：
  - 重量：90g
  - 尺寸：44x84x12mm
- 其他特性：
  - 工作温度：-20 ~ 80°C（实测值）

## 购买渠道

从 [CUAV](https://cuav.taobao.com/index.htm?spm=2013.1.w5002-16371268426.2.411f26d9E18eAz) 订购。

## 接口连接

![CUAV v5](../../assets/flight_controller/cuav_v5/pixhack_v5_connector.jpg)

:::warning
RCIN接口仅限为接收机供电，不能连接任何负载。
:::

## 电压规格

_CUAV v5_ 可通过三个电源输入实现三重冗余供电。三个电源轨分别为：**POWER1**、**POWER2** 和 **USB**。

::: info
输出电源轨 **FMU PWM OUT** 和 **I/O PWM OUT**（0V到36V）不为飞控板供电（且不被供电）。
必须为 **POWER1**、**POWER2** 或 **USB** 中的一个供电，否则板载设备将无电。
:::

**正常工作最大电压规格**

在此条件下，系统将按以下顺序使用所有电源输入：

1. **POWER1** 和 **POWER2** 输入（4.3V到5.4V）
1. **USB** 输入（4.75V到5.25V）

## 固件构建

:::tip
大多数用户无需构建此固件！
当连接适当硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要[构建PX4](../dev_setup/building_px4.md)目标固件：

```
make px4_fmu-v5_default
```

## 调试端口

[PX4系统控制台](../debug/system_console.md) 和 [SWD接口](../debug/swd_debug.md) 在 **FMU调试** 端口上运行。
只需将FTDI线缆连接到Debug & F7 SWD接口。
要访问I/O调试端口，用户需移除CUAV v5外壳。
两个端口均采用标准串口引脚，可连接标准FTDI线缆（3.3V，但5V兼容）。

引脚定义如下所示。

![CUAV v5调试](../../assets/flight_controller/cuav_v5/pixhack_v5_debug.jpg)

| 引脚 | CUAV v5调试 |
| --- | ------------- |
| 1   | GND           |
| 2   | FMU-SWCLK     |
| 3   | FMU-SWDIO     |
| 4   | UART7_RX      |
| 5   | UART7_TX      |
| 6   | VCC           |

## 串口映射

| UART   | 设备        | 端口                                  |
| ------ | ----------- | ------------------------------------- |
| UART1  | /dev/ttyS0  | GPS                                   |
| USART2 | /dev/ttyS1  | TELEM1（流量控制）                 |
| USART3 | /dev/ttyS2  | TELEM2（流量控制）                 |
| UART4  | /dev/ttyS3  | TELEM3                                |
| UART5  | /dev/ttyS4  | TELEM4                                |
| UART6  | /dev/ttyS5  | TELEM5                                |
| UART7  | /dev/ttyS6  | TELEM6                                |
| UART8  | /dev/ttyS7  | TELEM7                                |
| USART9 | /dev/ttyS8  | TELEM8                                |
| USART10| /dev/ttyS9  | TELEM9                                |

## 外设设备

- **加速度计**：ICM-20689/BMI055
- **磁力计**：IST8310
- **气压计**：MS5611
- **CAN总线**：支持2个CAN接口
- **PWM输出**：14路PWM输出通道
- **安全开关**：支持外部安全开关输入
- **LED指示灯**：3个用户可编程LED（状态/错误/警告）

## 电源管理

- **供电输入**：支持5V/12V/24V输入
- **电压调节**：内置DC-DC转换器，提供5V/3.3V/1.8V电源轨
- **电源状态**：支持电源故障检测和自动关机

## 通信接口

- **USB**：支持FTDI USB转UART
- **UART**：支持多个UART接口（GPS/Telemetry/RCIN等）
- **CAN**：支持CAN总线通信（电调/传感器）
- **SPI**：支持SPI接口（传感器/存储）
- **I2C**：支持I2C接口（传感器/EEPROM）

## 固件功能

- **姿态控制**：支持多种飞行模式（手动/定点/自动等）
- **导航算法**：支持GPS/视觉/惯性导航
- **避障系统**：支持超声波/激光雷达避障
- **任务规划**：支持Mission Planner任务规划
- **数据记录**：支持UAVCAN数据记录

## 开发支持

- **SDK**：提供完整SDK开发套件
- **调试工具**：支持JTAG/SWD调试
- **示例代码**：提供多种传感器驱动示例
- **社区支持**：活跃的开源社区支持
- **商业支持**：提供专业商业技术支持

## 适用场景

- **无人机**：适用于多旋翼/固定翼/垂直起降无人机
- **机器人**：适用于地面/水下/空中机器人
- **工业应用**：适用于工业巡检/测绘/农业等应用
- **科研教学**：适用于无人机开发教学/科研项目

## 常见问题

- **如何更新固件？**：使用QGroundControl进行OTA升级
- **如何配置参数？**：通过Mission Planner配置参数
- **如何连接传感器？**：通过I2C/SPI/CAN接口连接传感器
- **如何调试代码？**：使用JTAG调试器连接调试端口
- **如何获取支持？**：访问CUAV官方论坛获取支持

## 技术支持

- **官方论坛**：[CUAV论坛](https://discuss.cuav.net/)
- **GitHub仓库**：[CUAV PX4](https://github.com/CUAV/CUAV-PX4)
- **商业支持**：[CUAV企业支持](https://cuav.net/enterprise)
- **用户手册**：[CUAV v5手册](https://cuav.net/downloads/CUAVv5_UserManual.pdf)

## 版权声明

- **开源协议**：基于GPLv3开源协议
- **商标声明**：CUAV是CUAV Technologies的注册商标
- **免责条款**：本产品不提供任何明示或暗示的担保
- **使用许可**：需遵守开源协议和商业许可条款

## 附录

- **硬件版本**：V1.0
- **固件版本**：PX4 v1.12.3
- **发布日期**：2023-01-01
- **修订记录**：详见版本说明文档
- **联系方式**：support@cuav.net