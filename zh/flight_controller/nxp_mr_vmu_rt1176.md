

# NXP MR-VMU-RT1176 飞行控制器

<Badge type="tip" text="PX4 v1.15" />

:::warning
PX4 未制造此（或任何）自动驾驶仪。
如需硬件支持（https://community.nxp.com/）或合规性问题，请联系[制造商](https://www.nxp.com)。
:::

_MR-VMU-RT1176_ 参考设计基于 [Pixhawk<sup>&reg;</sup> FMUv6X-RT 开放标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-020%20Pixhawk%20Autopilot%20v6X-RT%20Standard.pdf)，这是 Pixhawk<sup>&reg;</sup> 飞行控制器系列的最新更新版本。

这是 NXP 针对 FMUv6X-RT 的开源_参考设计_，由行业合作伙伴<sup>&reg;</sup>、NXP 移动机器人团队和 PX4 团队共同设计制造。
作为参考/评估设计，该设计旨在被他人复制、修改或集成到量产中。
该产品已通过 FCC/CE ROHS REACH UKCA、EMI/RFI ESD 认证并在全球范围内提供
多家第三方制造商（如 Holybro.com）提供此产品或衍生商业产品。

![MR-VMU-RT1176 正视图](../../assets/flight_controller/nxp_mr-vmu-rt1176/mr-vmu-rt1176_upleft.jpg)

该电路板包含与 Pixhawk 6X-RT 配置相同的 FMU 模块，并搭配基于 NXP 的载板。
载板提供 100Base-T1（双线）汽车以太网、NFC 天线（连接到 SE051）和第三个 CAN 总线。
同时移除了 IO 处理器以启用 12 个 PWM 接口，其中 8 个支持 Dshot 功能。

该电路板利用了多个 Pixhawk​​® 开放标准，例如 FMUv6X-RT 标准、[自动驾驶仪总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 和 [连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。
配备高性能 NXP i.mx RT1176 双核处理器、模块化设计、三重冗余、温度控制的 IMU 板、隔离的传感器域，提供惊人的性能、可靠性和灵活性。

:::tip
PX4 维护和测试团队[支持](../flight_controller/autopilot_pixhawk_standard.md)此自动驾驶仪。
:::

## 介绍

在 MR-VMU-RT1176 模块内部，搭载了 NXP i.MX RT1176 处理器，配合来自 Bosch® 和 InvenSense® 的传感器技术，为控制任何自主机体提供了灵活性和可靠性，适用于学术和商业应用。

Pixhawk® 6X-RT 的 i.MX RT1176 Crossover 双核 MCU 包含运行频率高达 1GHz 的 Arm® Cortex®-M7 内核和运行频率高达 400MHz 的 Arm® Cortex®-M4 内核，配备 2MB SRAM 和 64MB 外部 XIP Flash。PX4 自动驾驶仪充分利用了增强的处理能力和 RAM。得益于这种增强的处理能力，开发者可以更高效地进行开发工作，实现复杂的算法和模型。

FMUv6X-RT 开放标准集成了高性能、低噪声的 IMU，设计用于提升稳定性。三重冗余 IMU 和双重冗余气压计通过独立总线运行。当 PX4 检测到传感器故障时，系统会无缝切换至另一个传感器以保持飞行控制的可靠性。

每个传感器组均通过独立的 LDO 供电，并具备独立电源控制。振动隔离系统可过滤高频振动并减少噪声，确保读数准确，使机体实现更优的飞行性能。

外部传感器总线（SPI5）提供两条片选线和数据就绪信号，支持附加的 SPI 接口传感器和载荷。结合集成的 Microchip 以太网物理层芯片，现在可通过以太网实现与任务计算机的高速通信。

MR-VMU-RT1176 参考设计非常适合企业研发实验室、初创公司、学术机构（研究、教授、学生）以及希望尝试 T1（双线）汽车以太网的商业应用。

请注意，由于这是一个参考设计，该特定电路板并未进行大批量生产。类似版本将由我们的授权厂商提供。

## 核心设计要点

- 高性能 [NXP i.MX RT1170 1GHz Crossover MCU](https://www.nxp.com/products/processors-and-microcontrollers/arm-microcontrollers/i-mx-rt-crossover-mcus/i-mx-rt1170-1-ghz-crossover-mcu-with-arm-cortex-cores:i.MX-RT1170) 配备 Arm® Cortex® 核心
- 硬件安全元件 [NXP EdgeLock SE051](https://www.nxp.com/products/security-and-authentication/authentication/edgelock-se051-proven-easy-to-use-iot-security-solution-with-support-for-updatability-and-custom-applets:SE051)。  
  这是广泛可信的 EdgeLock SE050 Plug & Trust 安全元件家族的扩展，支持现场更新小程序，提供经认证的 CC EAL 6+ 安全等级（AVAVAN.5 认证），可保障操作系统级别的安全防护，应对最新攻击场景。  
  可用于安全存储操作员 ID 或证书等敏感信息。
- 模块化飞控系统：独立的 IMU、FMU 和基础系统，通过 100 针和 50 针 Pixhawk® Autopilot Bus 接口连接
- 冗余设计：3 个 IMU 传感器和 2 个气压计传感器分别位于独立总线上
- 三重冗余域：完全隔离的传感器域，配备独立总线和独立电源控制
- 全新设计的减震系统可过滤高频振动并降低噪声，确保读数准确性
- 100Base-T1 双线以太网接口，支持高速任务计算机集成
- IMU 通过机载加热电阻实现温度控制，可达到最佳工作温度

### 处理器与传感器

- FMU处理器: NXP i.MX RT1176  
  - 32位 Arm® Cortex®-M7，1GHz  
  - 32位 Arm® Cortex®-M4，400MHz 二级核心  
  - 64MB 外部闪存  
  - 2MB RAM  

- NXP EdgeLock SE051 硬件安全元件  
  - 符合IEC62443-4-2认证要求  
  - 46KB 用户内存（可扩展至104KB）  
  - 面向物联网部署的CC EAL6+认证解决方案  
  - 支持AES和3DES加密/解密  

- 机载传感器  
  - 加速度计/陀螺仪：ICM-20649 或 BMI088  
  - 加速度计/陀螺仪：ICM-42688-P  
  - 加速度计/陀螺仪：ICM-42670-P  
  - 磁力计：BMM150  
  - 气压计：2x BMP388

### 电气数据

- 电压额定值：
  - 最大输入电压：6V
  - USB电源输入：4.75～5.25V
  - 舵机供电输入：0～36V
- 电流额定值：
  - `TELEM1`输出电流限制器：1.5A
  - 其余所有端口合并输出电流限制器：1.5A

### 机械数据

- 尺寸
  - 飞控模块：38.8 x 31.8 x 14.6mm
  - 标准底板：50 x 96 x 16.7mm
- 重量
  - 飞控模块：23g
  - 标准底板：51g

### 接口

- 12个PWM舵机输出，其中8个支持D-SHOT
- 遥控输入支持Spektrum/DSM
- 专用PPM和S.Bus输入的遥控接收机接口
- 专用模拟/PWM接收信号强度输入及S.Bus输出
- 4个通用串口：
  - 3个支持全流控制
  - 1个单独1.5A电流限制（Telem1）
  - 1个集成I2C及额外GPIO线的外接NFC读卡器
- 2个GPS端口：
  - 1个完整GPS加安全开关端口
  - 1个基础GPS端口
- 1个I2C端口
- 1个以太网端口：
  - 无变压器应用
  - 100Mbps
- 1个SPI总线：
  - 2条芯片选择线
  - 2条数据准备线
  - 1条SPI同步线
  - 1条SPI复位线
- 3个CAN总线外设接口（PX4当前支持2个）：
  - CAN总线支持独立静默控制或电调RX-MUX控制
- 2个带SMBus的电源输入端口
- 1个AD & IO端口
- 2个额外模拟输入
- 1个PWM/捕获输入
- 2条专用调试及GPIO线

- 其他特性：
  - 工作及存储温度：-40 ~ 85°C

## 购买渠道

从[NXP](https://www.nxp.com)订购。

## 组装/设置

接线方式类似于 [Holybro Pixhawk 6X](../flight_controller/pixhawk6x.md#connections) 和其他遵循 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 的板载方式。

<!-- TBD - provide sample wiring diagram. -->

## 连接

_MR-VMU-RT1176_ 连接器(遵循 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf))

![MR-VMU-RT1176 顶部视图](../../assets/flight_controller/nxp_mr-vmu-rt1176/mr-vmu-rt1176_top.jpg)
![MR-VMU-RT1176 正面视图](../../assets/flight_controller/nxp_mr-vmu-rt1176/mr-vmu-rt1176_front.jpg)
![MR-VMU-RT1176 左侧视图](../../assets/flight_controller/nxp_mr-vmu-rt1176/mr-vmu-rt1176_left.jpg)
![MR-VMU-RT1176 右侧视图](../../assets/flight_controller/nxp_mr-vmu-rt1176/mr-vmu-rt1176_right.jpg)

更多信息请参见:

- [NXP MR-VMU-RT1176 底板连接](https://nxp.gitbook.io/vmu-rt1176/production-v1-carrier-board-connectors) (nxp.gitbook.io)

## 引脚分配

[NXP MR-VMU-RT1176 主板引脚分配](https://nxp.gitbook.io/vmu-rt1176/pin-out) (nxp.gitbook.io)

说明：

- [相机捕获针脚](../camera/fc_connected_camera.md#camera-capture-configuration) (`PI0`) 是 AD&IO 端口上的第2针脚，在上方标记为 `FMU_CAP1`。

## 串口映射

| 串口   | 设备       | 端口     |
| ------ | ---------- | -------- |
| UART1  | /dev/ttyS0 | 调试    |
| UART3  | /dev/ttyS1 | GPS      |
| UART4  | /dev/ttyS2 | TELEM1   |
| UART5  | /dev/ttyS3 | GPS2     |
| UART6  | /dev/ttyS4 | PX4IO    |
| UART8  | /dev/ttyS5 | TELEM2   |
| UART10 | /dev/ttyS6 | TELEM3   |
| UART11 | /dev/ttyS7 | External |

<!--

## 尺寸

TBD
-->

## 电压等级

_MR-VMU-RT1176_ 的电源可以实现三重冗余，前提是提供三个电源。三个电源轨分别是：**POWER1**、**POWER2** 和 **USB**。MR-VMU-RT1176 上的 **POWER1** 和 **POWER2** 端口使用的是六路 [2.00mm 节距 CLIK-Mate 线对板 PCB 插座](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670)。

### 正常操作最大额定值

在此条件下系统将按以下顺序使用所有电源供电：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

### 绝对最大额定值

在这些条件下，系统将不会消耗任何电力（处于非工作状态），但硬件将保持完好。

1. **POWER1** 和 **POWER2** 输入（工作范围4.1V至5.7V，0V至10V无损）
1. **USB** 输入（工作范围4.1V至5.7V，0V至6V无损）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V至42V无损）

### 电压监控

数字I2C电池监控默认启用（参见[快速入门 > 电源](../assembly/quick_start_pixhawk6x.md#power)）。

::: info
通过ADC的模拟电池监控在此特定板上不支持，但在具有不同底板的此飞控的变种中可能支持。
:::

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接适当硬件时，它会由_QGroundControl_预构建并自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以针对此目标：

```sh
make px4_fmu-v6xrt_default
```

## 调试端口 {#debug_port}

PX4 [系统控制台](../debug/system_console.md) 和 [SWD 接口](../debug/swd_debug.md) 运行在 **FMU Debug** 端口上。

该接口的引脚定义和连接器符合 [Pixhawk Debug Full](../debug/swd_debug.md#pixhawk-debug-full) 接口标准，该标准定义在 [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 中（JST SM10B 连接器）。

| 针脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1 (红色)  | `Vtref`          | +3.3V |
| 2 (黑色)  | 控制台 TX (OUT) | +3.3V |
| 3 (黑色)  | 控制台 RX (IN)  | +3.3V |
| 4 (黑色)  | `SWDIO`          | +3.3V |
| 5 (黑色)  | `SWCLK`          | +3.3V |
| 6 (黑色)  | `SWO`            | +3.3V |
| 7 (黑色)  | NFC GPIO         | +3.3V |
| 8 (黑色)  | PH11             | +3.3V |
| 9 (黑色)  | nRST             | +3.3V |
| 10 (黑色) | `GND`            | GND   |

关于该端口的使用说明，请参考：

- [SWD 调试端口](../debug/swd_debug.md)
- [PX4 系统控制台](../debug/system_console.md)（注意，FMU 控制台映射到 USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机架

任何可以通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼 / 固定翼 / 地面车或船只。
所有支持的配置可以在 [机架参考](../airframes/airframe_reference.md) 中查看。

## 其他信息

- [更新Pixhawk 6X-RT引导加载程序](../advanced_config/bootloader_update_v6xrt.md)
- [Pixhawk 6X 接线快速入门](../assembly/quick_start_pixhawk6x.md)
- [PM02D 电源模块](../power_module/holybro_pm02d.md)
- [PM03D 电源模块](../power_module/holybro_pm03d.md)
- [Pixhawk FMUv6X-RT 开放标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-020%20Pixhawk%20Autopilot%20v6X-RT%20Standard.pdf)
- [Pixhawk 自动驾驶总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).