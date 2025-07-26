

# Holybro Pixhawk 5X

:::warning
PX4 does not manufacture this (or any) autopilot.
Contact the [manufacturer](https://holybro.com/) for hardware support or compliance issues.
:::

_Pixhawk 5X_<sup>&reg;</sup> 是 Pixhawk® 飞控家族的最新升级产品，由 Holybro<sup>&reg;</sup> 和 PX4 团队联合设计制造。

该产品基于 [Pixhawk​​® Autopilot FMUv5X Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf)、[Autopilot Bus Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf) 和 [Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)。
设备预装最新版 PX4 Autopilot®，具备三重冗余、温度控制、隔离传感器域，提供卓越的性能和可靠性。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_hero_upright.jpg" width="250px" title="Pixhawk5x Upright Image" /> <img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_exploded_diagram.jpg" width="450px" title="Pixhawk5x Exploded Image" />

:::tip
该飞控系统已获得 [PX4 维护和测试团队](../flight_controller/autopilot_pixhawk_standard.md) 的支持认证。
:::

## 介绍

Pixhawk® 5X 内部搭载基于 STMicroelectronics® 的 STM32F7 微控制器，配合来自 Bosch® 和 InvenSense® 的传感器技术，为控制任何自主机体提供灵活性和可靠性，适用于学术研究及商业应用。  
Pixhawk® 5X 的 F7 微控制器配备 2MB 闪存和 512KB RAM，PX4 自主飞行控制器充分利用了增强的处理能力和内存。  
得益于升级的处理性能，开发者可以更高效地完成开发工作，从而支持更复杂的算法和模型。

FMUv5X 开放标准集成了高性能、低噪声的惯性测量单元（IMU），旨在实现更优的稳定性能。  
三重冗余 IMU 与双重冗余气压计通过独立总线运行。当 PX4 自主飞行控制器检测到传感器故障时，系统会无缝切换至备用传感器，以保持飞行控制的可靠性。

每个传感器组均通过独立的 LDO 供电，实现独立电源控制。  
全新设计的振动隔离结构可过滤高频振动并减少噪声，确保测量精度，使机体实现更优的飞行性能。  
外部传感器总线（SPI5）配备两条芯片选择线和数据就绪信号，支持通过 SPI 接口扩展传感器和载荷设备。  
内置 Microchip 以太网 PHY（LAN8742AI-CZ-TR）模块，现可通过以太网实现与任务计算机的高速通信。  
两个智能电池监控接口（SMBus），支持 INA226 SMBus 电源模块。

Pixhawk® 5X 是企业研发实验室、初创公司、学术机构（科研人员、教授、学生）以及商业应用开发者的理想选择。

## 关键设计点

- 模块化飞控系统
  - 独立的IMU、FMU和基础系统通过100针与50针Pixhawk® Autopilot Bus接口连接，设计为灵活可定制的系统
- 冗余设计
  - 3个IMU传感器和2个气压计传感器分布在不同总线上，即使发生硬件故障也能实现并行连续运行
- 三重冗余域
  - 完全隔离的传感器域，具有独立总线和独立电源控制
- 温度控制IMU
  - 板载IMU加热电阻，确保IMU的最佳工作温度
- 振动隔离系统
  - 新设计的系统可过滤高频振动并减少噪声，确保读数准确
- 以太网接口
  - 用于高速任务计算机集成
- 自动传感器校准消除信号波动和温度变化影响
- 通过SMBus监控两个智能电池
- 为外部NFC读卡器提供额外的GPIO线和5V电源
- 用于无人机安全认证的安全元件（SE050）

## 技术规格

- FMU处理器: STM32F765
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM
- IO处理器: STM32F100
  - 32位Arm® Cortex®-M3，24MHz，8KB SRAM
- 机载传感器:

  - 加速度计/陀螺仪: ICM-20649
  - 加速度计/陀螺仪: ICM-42688P
  - 加速度计/陀螺仪: ICM-20602
  - 磁力计: BMM150
  - 气压计: 2x BMP388

- 接口

  - 16路PWM舵机输出
  - 遥控输入（Spektrum/DSM）
  - 专用PPM和S.Bus输入的遥控输入
  - 专用模拟/PWM RSSI输入和S.Bus输出
  - 4个通用串口
    - 3个带完整流控
    - 1个带独立1.5A电流限制
    - 1个带I2C和额外GPIO线（用于外接NFC读卡器）
  - 2个GPS端口
    - 1个完整GPS&安全开关端口
    - 1个基础GPS端口
  - 1个I2C端口
  - 1个以太网端口
    - 无变压器应用
  - 100Mbps
  - 1个SPI总线
  - 2个片选线
  - 2个数据就绪线
  - 1个SPI同步线
  - 1个SPI复位线
  - 2个CAN总线（用于CAN外设）
    - CAN总线支持独立静音控制或电调接收器复用控制
  - 2个SMBus供电输入端口
  - 1个AD&IO端口
    - 2个额外模拟输入
    - 1个PWM/捕获输入
    - 2个专用调试和GPIO线

- 电压规格

  - 最大输入电压: 6V
  - USB供电输入: 4.75~5.25V
  - 舵机供电输入: 0~36V

- 尺寸

  - 飞控模块: 38.8 x 31.8 x 14.6mm
  - 标准底板: 52.4 x 103.4 x 16.7mm

- 重量

  - 飞控模块: 23g
  - 标准底板: 51g

- 其他特性:
  - 工作及存储温度: -40 ~ 85°c

## 去哪购买

可在 [Holybro](https://holybro.com/products/pixhawk-5x) 购买。

## 组装/设置

[Pixhawk 5X接线快速入门](../assembly/quick_start_pixhawk5x.md) 提供了组装所需的/重要的外围设备（如GPS、电源模块等）的说明。

## 连接

![Pixhawk 5x 接线概览图](../../assets/flight_controller/pixhawk5x/pixhawk5x_wiring_diagram.jpg)

## 引脚配置

![Pixhawk 5X Pinout](../../assets/flight_controller/pixhawk5x/pixhawk5x_pinout.png)

::: info
连接器引脚分配是从左到右的（即 Pin 1 是最左侧的引脚）。
::: infos:

- [相机捕获引脚](../camera/fc_connected_camera.md#camera-capture-configuration) (`PI0`) 是 AD&IO 端口的第 2 引脚，上方标记为 `FMU_CAP1`。
- _Pixhawk 5X_ 引脚配置可从 [此处](https://github.com/PX4/PX4-user_guide/blob/main/assets/flight_controller/pixhawk5x/pixhawk5x_pinout.pdf) 或 [此处](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Pinout.pdf) 以 PDF 格式下载。

## 串口映射

| UART   | 设备         | 端口           |
| ------ | ------------ | -------------- |
| USART1 | /dev/ttyS0  | GPS           |
| USART2 | /dev/ttyS1  | TELEM3        |
| USART3 | /dev/ttyS2  | 调试控制台     |
| UART4  | /dev/ttyS3  | UART4和I2C     |
| UART5  | /dev/ttyS4  | TELEM2        |
| USART6 | /dev/ttyS5  | PX4IO/RC      |
| UART7  | /dev/ttyS6  | TELEM1        |
| UART8  | /dev/ttyS7  | GPS2          |

## 尺寸

![Pixhawk 5X 尺寸](../../assets/flight_controller/pixhawk5x/pixhawk5x_dimensions_all.jpg)

## 电压规格

_Pixhawk 5X_ 可通过三个供电源实现电源三重冗余。三个供电轨道分别为：**POWER1**、**POWER2** 和 **USB**。
Pixhawk 5X 的 **POWER1** 和 **POWER2** 接口采用 [2.00mm Pitch CLIK-Mate Wire-to-Board PCB Receptacle](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670) 连接器（6个电路）。

**正常运行最大电压**

在以下条件下，系统将按顺序使用所有供电源：

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）
1. **USB** 输入（4.75V 至 5.25V）

**绝对最大电压**

在以下条件下，系统将不会消耗任何电力（处于非工作状态），但硬件可保持完好：

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 不会损坏）
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 不会损坏）
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 不会损坏）

**电压监控**

数字I2C电池监控默认启用（详见 [Quickstart > Power](../assembly/quick_start_pixhawk5x.md#power)）。

::: info
通过ADC的模拟电池监控在此特定板上不受支持，但某些使用不同底板的飞行控制器变种可能支持。
:::

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适的硬件时，_QGroundControl_ 会自动安装预构建的固件。
:::

要[构建PX4](../dev_setup/building_px4.md)：

```
make px4_fmu-v5x_default
```

<a id="debug_port"></a>

## 调试端口

[PX4 System Console](../debug/system_console.md) 和 [SWD interface](../debug/swd_debug.md) 运行在 **FMU调试** 端口上。

该引脚定义和连接器符合 [Pixhawk Debug Full](../debug/swd_debug.md#pixhawk-debug-full) 接口规范，该规范在 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 接口中定义（JST SM10B连接器）。

| 引脚      | 信号           | 电压  |
| -------- | ---------------- | ----- |
| 1（红色）  | `Vtref`          | +3.3V |
| 2（黑色）  | 控制台TX（输出） | +3.3V |
| 3（黑色）  | 控制台RX（输入） | +3.3V |
| 4（黑色）  | `SWDIO`          | +3.3V |
| 5（黑色）  | `SWCLK`          | +3.3V |
| 6（黑色）  | `SWO`            | +3.3V |
| 7（黑色）  | NFC GPIO         | +3.3V |
| 8（黑色）  | PH11             | +3.3V |
| 9（黑色）  | nRST             | +3.3V |
| 10（黑色） | `GND`            | 接地   |

有关使用此端口的信息，请参见：

- [SWD Debug Port](../debug/swd_debug.md)
- [PX4 System Console](../debug/system_console.md)（注意，FMU控制台映射到USART3）

## 外设

- [数字空速传感器](https://holybro.com/products/digital-air-speed-sensor)
- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机型

任何可以通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼机/固定翼飞机/地面车或船只。所有支持的配置列表可以在[Airframes Reference](../airframes/airframe_reference.md)中查看。

## 进一步信息

- [Pixhawk 5X 接线快速入门](../assembly/quick_start_pixhawk5x.md)
- [Pixhawk 5X 概述与规格](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Spec_Overview.pdf) (Holybro)
- [Pixhawk 5X 引脚分配](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Pinout.pdf) (Holybro)
- [FMUv5X 参考设计引脚图](https://docs.google.com/spreadsheets/d/1Su7u8PHp-Y1AlLGVuH_I8ewkEEXt_bHHYBHglRuVH7E/edit#gid=562580340).
- [Pixhawk 自主飞行器FMUv5X标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf).
- [Pixhawk 自主飞行器总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).