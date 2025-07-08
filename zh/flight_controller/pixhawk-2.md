# Hex Cube Black飞行控制器

:::warning
PX4不生产此（或任何）自动驾驶仪。
关于硬件支持或合规性问题，请联系[制造商](https://cubepilot.org/#/home)。
:::

:::tip
[Cube Orange](cubepilot_cube_orange.md)是该产品的后续产品。
我们建议考虑基于行业标准的产品，例如[Pixhawk标准](autopilot_pixhawk_standard.md)。
此飞行控制器不遵循标准，使用专利连接器。
:::

[Hex Cube Black](http://www.proficnc.com/61-system-kits2)飞行控制器（以前称为Pixhawk 2.1）是一个灵活的自动驾驶仪，主要面向商业系统制造商。
它基于[Pixhawk-project](https://pixhawk.org/) **FMUv3** 开源硬件设计，并在[NuttX](https://nuttx.apache.org/)操作系统上运行PX4。

![Cube Black](../../assets/flight_controller/cube/cube_black_hero.png)

该控制器设计为与特定领域的载板配合使用，以减少布线、提高可靠性和简化组装。
例如，商用检测机体的载板可能包含连接伴飞计算机的接口，而竞速机体的载板可能包含来自机体框架的电调。

Cube在两个IMU上采用减振设计，第三个IMU固定作为参考/备份。

::: info
制造商[Cube Docs](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview)包含详细信息，包括关于[Cube颜色差异](https://docs.cubepilot.org/user-guides/autopilot/the-cube-module-overview#differences-between-cube-colours)的概述。
:::

:::tip
此自动驾驶仪由PX4维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 核心特性

- 32位STM32F427 [Cortex-M4F](http://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M4) 带FPU的处理器
- 168 MHz / 252 MIPS
- 256 KB RAM
- 2 MB Flash（完全可用）
- 32位STM32F103安全处理器
- 14路PWM/舵机输出（8路带安全功能和手动覆盖，6路辅助，高功率兼容）
- 丰富的外设连接选项（UART、I2C、CAN）
- 集成备份系统，用于飞行中恢复和手动覆盖，采用专用处理器和独立电源（固定翼用途）
- 备份系统集成混合功能，提供一致的自动驾驶仪和手动覆盖混合模式（固定翼用途）
- 冗余电源输入和自动故障转移
- 外部安全开关
- 多色LED主视觉指示器
- 高功率多音调压电音频指示器
- microSD卡用于长时间高频率数据记录

<a id="stores"></a>

## 购买渠道

[Cube Black](http://www.proficnc.com/61-system-kits)（ProfiCNC）

## 组装

[Cube接线快速入门](../assembly/quick_start_cube.md)

## 技术参数

### 处理器

- 32位STM32F427 [Cortex M4](http://en.wikipedia.org/wiki/ARM_Cortex-M#Cortex-M4) 带FPU的处理器
- 168 MHz / 252 MIPS
- 256 KB RAM
- 2 MB Flash（完全可用）
- 32位STM32F103安全协处理器

### 传感器

- TBA

### 接口

- 5路UART（串口），其中一路高功率兼容，2路带硬件流控制
- 2路CAN（一路带内部3.3V收发器，一路在扩展接口）
- Spektrum DSM/DSM2/DSM-X® 卫星兼容输入
- Futaba S.BUS® 兼容输入和输出
- PPM总和信号输入
- RSSI（PWM或电压）输入
- I2C
- SPI
- 3.3V ADC输入
- 内部和外部microUSB端口

### 电源系统与保护

- 理想二极管控制器带自动故障转移
- 舵机轨道高功率（最大10V）和高电流（10A+）兼容
- 所有外设输出过流保护，所有输入防静电保护

### 电压参数

如果提供三个电源，Pixhawk可以实现三重冗余供电。三个电源轨道为：电源模块输入、舵机轨道输入、USB输入。

#### 正常操作最大参数

在此条件下，所有电源将按以下顺序为系统供电：

- 电源模块输入（4.8V至5.4V）
- 舵机轨道输入（4.8V至5.4V）**最高可达10V用于手动覆盖，但若未连接电源模块输入且电压超过5.7V，自动驾驶仪部分将断电**
- USB电源输入（4.8V至5.4V）

#### 绝对最大参数

在此条件下，系统将不消耗任何功率（不运行），但会保持完好：

- 电源模块输入（4.1V至5.7V，0V至20V无损坏）
- 舵机轨道输入（4.1V至5.7V，0V至20V）
- USB电源输入（4.1V至5.7V，0V至6V）

## 引脚定义与原理图

电路板原理图和其他文档可在此处找到：[The Cube Project](https://github.com/proficnc/The-Cube)。

## 接口

### 顶部（GPS、TELEM等）

![Cube接口 - 顶部（GPS、TELEM等）和Main/AUX](../../assets/flight_controller/cube/cube_ports_top_main.jpg)

<a id="serial_ports"></a>

### 串口映射

| UART   | 设备       | 接口                  |
| ------ | ---------- | --------------------- |
| USART1 | /dev/ttyS0 | <!-- IO debug? -->    |
| USART2 | /dev/ttyS1 | TELEM1（带流控制）    |
| USART3 | /dev/ttyS2 | TELEM2（带流控制）    |
| UART4  | /dev/ttyS3 | GPS1                  |
| USART6 | /dev/ttyS4 | PX4              |
| UART7  | /dev/ttyS5 | AUX1                  |
| UART8  | /dev/ttyS6 | AUX2                  |

:::note
注：PX4串口默认映射到USART6，但可通过参数重新配置。
:::

### 电源接口

| 接口       | 描述                   |
| ---------- | ---------------------- |
| 5V_USB     | USB 5V电源             |
| 5V_SMB     | SMB 5V电源             |
| 3.3V_SMB   | SMB 3.3V电源           |
| VDD_5V     | 系统5V总线             |
| VDD_3V3    | 系统3.3V总线           |

## 固件配置

```bash
# 固件烧录命令示例
make px4_sitl_default
make px4_sitl_default upload
```

## 开发资源

- [PX4开发指南](https://docs.px4.io/master/en/)
- [CubeMX配置工具](https://www.st.com/en/development-tools/stm32cubemx.html)
- [STM32 HAL库](https://www.st.com/en/embedded-software/stm32-hal.html)

:::info
更多开发资源请访问[PX4官方文档](https://docs.px4.io/master/en/)。
:::