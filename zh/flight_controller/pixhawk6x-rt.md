# Holybro Pixhawk 6X-RT

:::warning
PX4 并不生产此款（或任何）自动驾驶仪。
如需硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

_Pixhawk 6X-RT_<sup>&reg;</sup> 是 Pixhawk® 飞控家族的最新升级产品，由 Holybro<sup>&reg;</sup> 与 NXP 移动机器人团队及 PX4 开发团队基于 NXP 开源参考设计联合设计制造。

该产品基于 [Pixhawk​​® Autopilot FMUv6X 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf)、[自动驾驶仪总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 和 [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。

配备高性能 NXP i.mx RT1176 双核处理器，采用模块化设计、三重冗余架构、温度控制的 IMU 板、隔离传感器域，提供卓越的性能、可靠性和灵活性。

<img src="../../assets/flight_controller/pixhawk6x-rt/pixhawk6x-rt.png" width="350px" title="Pixhawk6X-RT 正视图" /> <img src="../../assets/flight_controller/pixhawk6x/pixhawk6x_exploded_diagram.png" width="300px" title="Pixhawk6X 拆解图" />

:::tip
此自动驾驶仪已获得 PX4 维护和测试团队的[支持](../flight_controller/autopilot_pixhawk_standard.md)。
:::

## 引言

在Pixhawk® 6X-RT内部，您会发现NXP i.mx RT1176，搭配来自Bosch®和InvenSense®的传感器技术，为您提供控制任何自主机体的灵活性和可靠性，适用于学术和商业应用。

Pixhawk® 6X-RT的i.mx RT1176 Crossover双核MCU包含一个最高运行频率达1GHz的Arm® Cortex®-M7核心和一个最高运行频率达400MHz的Arm® Cortex®-M4核心，配备2MB SRAM和64MB外部XIP Flash。PX4自动驾驶仪充分利用了增强的算力和RAM。得益于更强的处理能力，开发者能够更高效地进行开发工作，从而实现复杂算法和模型。

FMUv6X开放标准包含高性能、低噪声IMU，设计用于更好的稳定控制。三重冗余IMU和双重冗余气压计分别在不同总线上。当PX4检测到传感器故障时，系统会无缝切换到其他传感器以保持飞行控制可靠性。

每个传感器集由独立的LDO供电并具有独立电源控制。振动隔离系统可过滤高频振动并降低噪声，确保读数准确，使机体实现更优的整体飞行性能。

外部传感器总线(SPI5)拥有两条芯片选择线和数据就绪信号，可连接SPI接口的额外传感器和载荷。集成Microchip以太网PHY后，可通过以太网实现与任务计算机的高速通信。

Pixhawk® 6X-RT非常适用于企业研发实验室、初创公司、学术机构（研究、教授、学生）及商业应用。

## 关键设计要点

- 高性能 [NXP i.MX RT1170 1GHz Crossover MCU](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/i-mx-rt-crossover-mcus/i-mx-rt1170-1-ghz-crossover-mcu-with-arm-cortex-cores:i.MX-RT1170) 配备 Arm® Cortex® 核心
- 硬件安全元件 [NXP EdgeLock SE051](https://www.nxp.com/products/security-and-authentication/authentication/edgelock-se051-proven-easy-to-use-iot-security-solution-with-support-for-updatability-and-custom-applets:SE051) 作为广泛信任的 EdgeLock SE050 Plug & Trust 安全元件家族扩展，支持现场更新小程序并提供经 CC EAL 6+ 认证的成熟安全方案，符合 AVA_VAN.5 标准直至操作系统层级，可抵御最新攻击场景。例如：安全存储操作员 ID 或证书
- 模块化飞控系统：分离式 IMU、FMU 和基板系统，通过 100 针和 50 针 Pixhawk® 自动飞行控制器总线连接
- 冗余设计：3 个 IMU 传感器和 2 个气压计传感器分布在独立总线上
- 三重冗余域：完全隔离的传感器域，配备独立总线和独立电源控制
- 新型振动隔离系统可过滤高频振动并降低噪声，确保测量精度
- 以太网接口用于高速任务计算机集成
- IMU 通过板载加热电阻实现温度控制，确保 IMU 的最佳工作温度

### 处理器与传感器

- FMU 处理器：NXP i.MX RT1176
  - 32 位 Arm® Cortex®-M7，1GHz
  - 32 位 Arm® Cortex®-M4，400MHz 从属核心
  - 64MB 外部闪存
  - 2MB RAM
- NXP EdgeLock SE051 硬件安全元件
  - 符合 IEC62443-4-2 标准的认证
  - 46kB 用户内存（可个性化扩展至 104kB）
  - 经认证的 CC EAL6+ 级别 IoT 安全解决方案
  - AES 和 3DES 加密解密功能
- IO 处理器：STM32F100
  - 32 位 Arm® Cortex®-M3，24MHz，8KB SRAM
- 板载传感器
  - 加速度计/陀螺仪：ICM-20649 或 BMI088
  - 加速度计/陀螺仪：ICM-42688-P
  - 加速度计/陀螺仪：ICM-42670-P
  - 磁力计：BMM150
  - 气压计：2 个 BMP388

### 电气参数

- 电压规格：
  - 最大输入电压：6V
  - USB 供电输入：4.75～5.25V
  - 舵机供电输入：0～36V
- 电流规格：
  - `TELEM1` 输出电流限制器：1.5A
  - 其他端口总输出电流限制器：1.5A

### 机械参数

- 尺寸
  - 飞控模块：38.8 x 31.8 x 14.6mm
  - 标准底板：52.4 x 103.4 x 16.7mm
  - 迷你底板：43.4 x 72.8 x 14.2 mm
- 重量
  - 飞控模块：23g
  - 标准底板：51g
  - 迷你底板：26.5g

### 接口

- 16 路 PWM 舵机输出
- 遥控输入（Spektrum/DSM）
- 专用遥控输入（PPM 和 S.Bus）
- 专用模拟/PWM RSSI 输入和 S.Bus 输出
- 4 个通用串口
  - 3 个支持全流量控制
  - 1 个带独立 1.5A 电流限制（Telem1）
  - 1 个带 I2C 和额外 GPIO 线用于外部 NFC 读卡器
- 2 个 GPS 接口
  - 1 个完整 GPS + 安全开关端口
  - 1 个基础 GPS 端口
- 1 个 I2C 接口
- 1 个以太网接口
  - 非变压器应用
  - 100Mbps
- 1 个 SPI 总线
  - 2 条芯片选择线
  - 2 条数据就绪线
  - 1 条 SPI 同步线
  - 1 条 SPI 复位线
- 2 条 CAN 总线用于 CAN 外设
  - CAN 总线支持独立静音控制或电调 RX-MUX 控制
- 2 个 SMBus 供电输入
  - 1 个 AD 与 IO 端口
  - 2 个额外模拟输入
  - 1 个 PWM/捕获输入
  - 2 条专用调试和 GPIO 线

- 其他特性：
  - 工作/存储温度：-40 ~ 85℃
  
  ## 购买地点

从 [Holybro](https://holybro.com/products/fmuv6x-rt-developer-edition) 下单购买。

## 组装/设置

[Pixhawk 6X 接线快速入门](../assembly/quick_start_pixhawk6x.md)提供了组装所需/重要外设的说明，包括GPS、电源模块等。

## 连接

示例布线图

![Pixhawk 6X Wiring Overview](../../assets/flight_controller/pixhawk6x/pixhawk6x_wiring_diagram.png)

## 引脚分配

- [Holybro Pixhawk Baseboard Pinout](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-baseboard-pinout)
- [Holybro Pixhawk Mini-Baseboard Pinout](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-mini-baseboard-pinout)

说明：

- [摄像头捕捉引脚](../camera/fc_connected_camera.md#camera-capture-configuration) (`PI0`) 位于AD&IO端口的第2针脚，上方标记为 `FMU_CAP1`。

## 串口映射

| UART   | 设备      | 端口     |
| ------ | --------- | -------- |
| UART1  | /dev/ttyS0 | 调试     |
| UART3  | /dev/ttyS1 | GPS      |
| UART4  | /dev/ttyS2 | TELEM1   |
| UART5  | /dev/ttyS3 | GPS2     |
| UART6  | /dev/ttyS4 | PX4IO    |
| UART8  | /dev/ttyS5 | TELEM2   |
| UART10 | /dev/ttyS6 | TELEM3   |
| UART11 | /dev/ttyS7 | 外部     |

## 尺寸

[Pixhawk 6X 尺寸](https://docs.holybro.com/autopilot/pixhawk-6x/dimensions)

## 电压规格

_Pixhawk 6X-RT_ 在提供三个电源时可实现电源三重冗余。三个电源轨为：**POWER1**、**POWER2** 和 **USB**。
Pixhawk 6X 上的 **POWER1** 与 **POWER2** 接口采用 6 电路 [2.00mm 间距 CLIK-Mate 线对板 PCB 插座](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670)。

**正常操作最大额定值**

在此条件下，系统将按以下顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大额定值**

在此条件下，系统将不取用任何电力（无法运行），但硬件仍可完好无损。

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 无损）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 无损）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 无损）

**电压监控**

数字 I2C 电池监控功能默认启用（参见 [Quickstart > Power](../assembly/quick_start_pixhawk6x.md#power)）。

::: info
通过 ADC 的模拟电池监控在此特定板上不支持，但具有不同底板的飞行控制器变体可能支持。
:::

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接适当的硬件时，它会由_QGroundControl_预编译并自动安装。
:::

要[构建 PX4](../dev_setup/building_px4.md)针对此目标：

```
make px4_fmu-v6xrt_default
```

<a id="debug_port"></a>

## 调试端口

[PX4系统控制台](../debug/system_console.md) 和 [SWD接口](../debug/swd_debug.md) 运行在 **FMU调试** 端口。

引脚定义和连接器符合 [Pixhawk调试完整](../debug/swd_debug.md#pixhawk-debug-full) 接口规范，该规范定义在 [Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 中（JST SM10B连接器）。

| 引脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1 (红色)  | `Vtref`          | +3.3V |
| 2 (黑色)  | 控制台TX（输出） | +3.3V |
| 3 (黑色)  | 控制台RX（输入） | +3.3V |
| 4 (黑色)  | `SWDIO`          | +3.3V |
| 5 (黑色)  | `SWCLK`          | +3.3V |
| 6 (黑色)  | `SWO`            | +3.3V |
| 7 (黑色)  | NFC GPIO         | +3.3V |
| 8 (黑色)  | PH11             | +3.3V |
| 9 (黑色)  | nRST             | +3.3V |
| 10 (黑色) | `GND`            | GND   |

关于使用此端口的更多信息请参见：

- [SWD调试端口](../debug/swd_debug.md)
- [PX4系统控制台](../debug/system_console.md)（注意：FMU控制台映射到USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机体

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或船。
所有支持的完整配置可参考 [机体参考](../airframes/airframe_reference.md)。

## 更多信息

- [更新Pixhawk 6X-RT引导加载程序](../advanced_config/bootloader_update_v6xrt.md)
- [Holybro Docs](https://docs.holybro.com/) (Holybro)
- [Pixhawk 6X接线快速入门](../assembly/quick_start_pixhawk6x.md)
- [PM02D电源模块](../power_module/holybro_pm02d.md)
- [PM03D电源模块](../power_module/holybro_pm03d.md)
- [Pixhawk自动驾驶仪FMUv6X标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf).
- [Pixhawk自动驾驶仪总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhaw k%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).