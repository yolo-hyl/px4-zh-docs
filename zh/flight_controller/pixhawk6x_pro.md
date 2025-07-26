# Holybro Pixhawk 6X Pro

:::warning
PX4 并不制造此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

![Pixhawk6X Pro 内部架构图](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_internal_architecture.jpeg)

## 关键设计要点

- 高性能 ADIS16470 工业 IMU（±40 g 动态加速度范围），适用于苛刻无人机应用中的高精度运动感知
- 全新先进耐用减振材料（共振频率位于高频段），适用于工业级和商用无人机应用
- 高性能 STM32H753 处理器
- 以太网接口，支持高速任务计算机集成

## 基板

Pixhawk 6X Pro 可根据不同的使用场景和机体类型选择多种基板（或不带基板），包括 [Standard v2A](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-baseboard-v2-ports)、[Standard v2B](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-baseboard-v2-ports) 和 [Mini](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-mini-baseboard-ports)，如下所示。
它也可以与其他符合Pixhawk Autopilot Bus (PAB) 规格的基板配合使用，例如 [Holybro Pixhawk Jetson Baseboard](../companion_computer/holybro_pixhawk_jetson_baseboard.md) 和 [Holybro Pixhawk RPi CM4 Baseboard](../companion_computer/holybro_pixhawk_rpi_cm4_baseboard.md)。

:::: tabs

::: tab Pixhawk 6X Pro Standard v2A

![Pixhawk 6X Pro Standard v2A](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_v2a.webp)

:::

::: tab Pixhawk 6X Pro Standard v2B

![Pixhawk 6X Pro Standard v2B](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_v2b.webp)
:::

::: tab Pixhawk 6X Pro Mini

![Pixhawk 6X Pro Mini](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_mini.webp)
:::

::::

## 功能特性

- 三重冗余IMU与双重冗余气压计，分别位于独立总线上
- 模块化飞控系统：分离式IMU、FMU和基板系统
- 安全驱动设计，整合不同厂商和型号的传感器
- 每组传感器均配备独立LDO电源，支持独立电源控制
- 温控IMU板，确保IMU工作在最佳温度

::: details 处理器与传感器

- FMU处理器：STM32H753
  - 32位Arm® Cortex®-M7，480MHz，2MB闪存，1MB RAM
- IO处理器：STM32F103
  - 32位Arm® Cortex®-M3，72MHz，64KB SRAM
- 板载传感器
  - 加速度计/陀螺仪：ADIS16470（±40g，减震隔离，工业级IMU）
  - 加速度计/陀螺仪：IIM-42652（±16g，减震隔离，工业级IMU）
  - 加速度计/陀螺仪：ICM-45686 with BalancedGyro™ Technology（±32g，固定安装）
  - 气压计：ICP20100
  - 气压计：BMP388
  - 磁力计：BMM150

:::

::: details 电气参数

- 电压规格：
  - 最大输入电压：6V
  - USB供电输入：4.75~5.25V
  - 舵机电压输入：0~36V
- 电流规格：
  - Telem1输出电流限制：1.5A
  - 其他端口总输出电流限制：1.5A

:::

::: details 机械参数

- 尺寸
  - 飞控模块：38.8 x 31.8 x 30.1mm
  - 标准基板：52.4 x 103.4 x 16.7mm
  - 迷你基板：43.4 x 72.8 x 14.2 mm
- 重量
  - 飞控模块：50g
  - 标准基板：51g
  - 迷你基板：26.5g

:::

::: details 接口配置

- 16路PWM舵机输出
- R/C输入支持Spektrum/DSM
- 专用PPM和S.Bus输入的R/C接口
- 独立模拟/PWM RSSI输入和S.Bus输出
- 4个通用串口
  - 3个支持全流量控制
  - 1个独立1.5A电流限制（Telem1）
  - 1个包含I2C和额外GPIO线的外部NFC读取器接口
- 2个GPS端口
  - 1个完整GPS加安全开关端口
  - 1个基础GPS端口
- 1个I2C端口
- 1个以太网端口
  - 无变压器设计
  - 100Mbps速率
- 1个SPI总线
  - 2条芯片选择线
  - 2条数据就绪线
  - 1条SPI同步线
  - 1条SPI复位线
- 2条CAN总线用于CAN外设
  - CAN总线支持独立静默控制或电调RX-MUX控制
- 2个SMBus供电输入
  - 1个AD与IO端口
  - 2个额外模拟输入
  - 1个PWM/捕获输入
  - 2条专用调试和GPIO线

:::

## 购买渠道

从 [Holybro](https://holybro.com/products/pixhawk-6x-pro) 购买。

## 组装/设置

[Pixhawk 6X Wiring Quick Start](../assembly/quick_start_pixhawk6x.md) 提供了关于如何组装所需/重要外设的说明，包括 GPS、电源模块等。

## 接线

### 多旋翼接线示例

![Pixhawk 6X Pro 接线概览](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_wiring_diagram.png)

### 垂直起降接线示例

![Pixhawk 6X Pro 垂直起降固定翼接线概览](../../assets/flight_controller/pixhawk6x_pro/pixhawk6x_pro_wiring_diagram_vtol.webp)

## 引脚分配

- [Holybro Pixhawk Baseboard 引脚分配](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-baseboard-pinout)
- [Holybro Pixhawk Mini-Baseboard 引脚分配](https://docs.holybro.com/autopilot/pixhawk-6x/pixhawk-mini-baseboard-pinout)
- [Holybro Pixhawk Jetson Baseboard](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-jetson-baseboard)
- [Holybro Pixhawk RPi CM4 Baseboard](https://docs.holybro.com/autopilot/pixhawk-baseboards/pixhawk-rpi-cm4-baseboard)

说明：

- 相机捕获引脚([camera capture pin](../camera/fc_connected_camera.md#camera-capture-configuration)) (`PI0`) 位于 AD&IO 端口的第2针脚，在图中标记为 `FMU_CAP1`。

## 串口映射

| 串口   | 设备     | 端口          |
| ------ | -------- | ------------- |
| USART1 | /dev/ttyS0 | GPS           |
| USART2 | /dev/ttyS1 | TELEM3        |
| USART3 | /dev/ttyS2 | Debug Console |
| UART4  | /dev/ttyS3 | UART4 & I2C   |
| UART5  | /dev/ttyS4 | TELEM2        |
| USART6 | /dev/ttyS5 | PX4IO/RC      |
| UART7  | /dev/ttyS6 | TELEM1        |
| UART8  | /dev/ttyS7 | GPS2          |

## 尺寸

[Pixhawk 6X Pro Dimensions](https://docs.holybro.com/autopilot/pixhawk-6x-pro/dimensions)

## 电压规格

_Pixhawk 6X Pro_ 在提供三个电源时可实现电源三重冗余。  
三个电源轨道分别为：**POWER1**、**POWER2** 和 **USB**。  
Pixhawk 6X 上的 **POWER1** 与 **POWER2** 接口采用 6 路 [2.00mm 节距 CLIK-Mate 线对板 PCB 插座](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670)。  

**正常操作最大规格**  

在以下条件下，系统将按以下顺序使用所有电源供电：  

1. **POWER1** 和 **POWER2** 输入（4.9V 至 5.5V）  
1. **USB** 输入（4.75V 至 5.25V）  

**绝对最大规格**  

在以下条件下，系统将不从任何电源取电（不可操作），但硬件保持完好：  

1. **POWER1** 和 **POWER2** 输入（工作范围 4.1V 至 5.7V，0V 至 10V 无损）  
1. **USB** 输入（工作范围 4.1V 至 5.7V，0V 至 6V 无损）  
1. 舵机输入：**FMU PWM OUT** 和 **I/O PWM OUT** 的 VDD_SERVO 引脚（0V 至 42V 无损）  

**电压监控**  

数字 I2C 电池监控默认启用（详见 [快速入门 > 电源](../assembly/quick_start_pixhawk6x.md#power)）。  

::: info  
通过 ADC 的模拟电池监控在此特定板上不支持，但可能在不同基板版本的此飞控变种中支持。  
:::

## 固件构建

:::tip
大多数用户无需构建此固件！
它会在连接适当硬件时由 _QGroundControl_ 自动预构建并安装。
:::

要[构建 PX4](../dev_setup/building_px4.md) 以针对此目标：

```
make px4_fmu-v6x_default
```

## 调试端口

[PX4 系统控制台](../debug/system_console.md)和[SWD 接口](../debug/swd_debug.md)运行在 **FMU Debug** 端口。

该引脚定义和连接器符合 [Pixhawk Debug Full](../debug/swd_debug.md#pixhawk-debug-full) 接口标准，该标准定义在 [Pixhawk Connector Standard](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 接口中（JST SM10B 连接器）。

| 引脚      | 信号           | 电压  |
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

关于使用该端口的信息请参见：

- [SWD 调试端口](../debug/swd_debug.md)
- [PX4 系统控制台](../debug/system_console.md)（注意，FMU 控制台映射到 USART3）

## 外设

- [遥测无线电模块](https://holybro.com/collections/telemetry-radios?orderby=date)
- [测距仪/距离传感器](../sensor/rangefinders.md)
- [Holybro 传感器](https://holybro.com/collections/sensors)
- [Holybro GPS & RTK 系统](https://holybro.com/collections/gps-rtk-systems)
- [电源模块 & 配电板](https://holybro.com/collections/power-modules-pdbs)

## 支持的平台/机型

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车辆或船只。  
完整的支持配置列表可参考[Airframes Reference](../airframes/airframe_reference.md)。

## CAD文件

下载 [这里](https://docs.holybro.com/autopilot/pixhawk-6x-pro/download)。

## 参见

- [Holybro 文档](https://docs.holybro.com/autopilot/pixhawk-6x-pro) (Holybro)
- [Pixhawk 6X 接线快速入门](../assembly/quick_start_pixhawk6x.md)
- [Pixhawk 飞控 FMUv6X 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-012%20Pixhawk%20Autopilot%20v6X%20Standard.pdf).
- [Pixhawk 飞控总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 接插件标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).