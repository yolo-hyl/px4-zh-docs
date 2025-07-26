

# Holybro Pixhawk Mini（已停产）

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

Holybro *Pixhawk<sup>&reg;</sup> Mini* 自动驾驶仪是 Pixhawk 的新一代演进版。
其体积约为原始 Pixhawk 的 1/3，并配备了更强大的处理器和传感器。

Pixhawk Mini 基于 PX4 开源硬件项目，已针对 PX4 飞行堆栈进行了优化。

![Pixhawk Mini](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_hero.jpg)

布线信息请参见[下方](#wiring)。

::: info
此飞控由 3DR 与 HobbyKing<sup>&reg;</sup> 合作设计。
其曾名为 3DR Pixhawk Mini。
:::

:::tip
此自动驾驶仪[受支持](../flight_controller/autopilot_pixhawk_standard.md) 由 PX4 维护和测试团队提供支持。
:::

## 规格参数

**处理器：**

- **主处理器：** STM32F427 Rev 3
- **IO处理器：** STM32F103

**传感器：**

- **加速度计/陀螺仪/磁力计：** MPU9250  
  - [已弃用](https://github.com/PX4/PX4-Autopilot/pull/7618)（由PX4固件）
- **加速度计/陀螺仪：** ICM20608
- **气压计：** MS5611

**电压规格：**

- **电源模块输出：** 4.1\~5.5V
- **最大输入电压：** 45V（10S锂电池）
- **最大电流检测：** 90A
- **USB电源输入：** 4.1\`5.5V
- **舵机电源输入：** 0\~10V

**接口：**

- 1 x UART串口（用于GPS）
- Spektrum DSM/DSM2/DSM-X® 卫星兼容遥控输入
- Futaba S BUS® 兼容遥控输入
- PPM总线信号遥控输入
- I2C（用于数字传感器）
- CAN（用于数字电机控制，需兼容控制器）
- ADC（用于模拟传感器）
- Micro USB接口

**重量与尺寸：**

- **尺寸：** 38x43x12mm
- **重量：** 15.8g

**GPS模块（套件标配）：**

- **GNSS接收器：** u-blox<sup>&reg;</sup> Neo-M8N；磁力计 HMC5983
- **重量：** 22.4g
- **尺寸：** 37x37x12mm

## 去哪里购买

已停产。

## 连接器分配

`<To be added>`

## 功能特性

Pixhawk Mini 的关键功能特性包括：

- 搭载先进 32 位 ARM Cortex® M4 处理器，运行 NuttX RTOS
- 8 个 PWM/舵机输出
- 多种外设连接选项（UART、I2C、CAN）
- 冗余电源输入及自动故障切换
- 内置安全开关及可选外接安全按钮，便于电机激活
- 多色 LED 指示灯
- 内置多音调压电音频提示器
- microSD 卡用于长时间高速日志记录
- 易于使用的 Micro JST 接口

Pixhawk Mini 配备全新的 **GPS 模块**：

- 基于 u-blox M8N
- 可同时接收最多 3 种 GNSS（GPS、伽利略、GLONASS、北斗）
- 行业领先的 -167 dBm 导航灵敏度
- 安全性与完整性保护
- 支持所有卫星增强系统
- 先进的干扰与欺骗检测
- 不同产品版本可满足性能和成本需求

## 套件内容包

_Pixhawk Mini_ 的套件包含以下内容：

| 组件名称                                                                 | 图片                                                                                                                                |
| ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Pixhawk Mini 自主飞行控制器                                              | ![Pixhawk Mini](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_drawing.png)                                                |
| GPS模块                                                                 | ![指南针+GPS模块](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_compass_drawing.png)                                  |
| 四通配电板                                                               | ![四通配电板](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_quad_power_distribution_board_drawing.png)               |
| 8通道PWM转接板                                                           | ![8通道PWM转接板](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_8_channel_pwm_breakout_board_drawing.png)             |
| 4针线缆（用于I2C）                                                       | ![4针线缆（用于I2C）](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_4_pin_cable_drawing.png)                           |
| PPM/SBUS信号接收线缆                                                     | ![RC输入线缆（用于PPM/SBUS）](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_rc_in_cable_drawing.png)                   |
| 6针转6/4针Y型适配器（用于GPS和附加I2C设备）                             | ![6针转6/4针Y型适配器](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_6_to_6_and_4_pin_Y_cable_drawing.png)             |
| 6针线缆（2条）（用于配电板和指南针/GPS）                                | ![6针线缆](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_6_pin_cable_drawing.png)                                      |
| 6针JST转DF13线缆（用于传统数传电台）                                   | ![6针JST转DF13](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_6pin_JST_to_DF13_cable_drawing.png)                     |
| 安全开关                                                               | ![安全开关](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_safety_switch_drawing.png)                                   |
| 8通道PWM转接线缆                                                       | ![8通道PWM转接线缆](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_8channel_pwm_breakout_cable_drawing.png)             |
| 安装泡沫垫                                                             | ![安装泡沫垫](../../assets/hardware/mounting/3dr_anti_vibration_mounting_foam.png)                                                |
| I2C转接板？ - 发放材料中未列出的部件                                   |  -                                                                                                                                   |

## 可选配件

- 遥测无线电套装：915 MHz（美国），433 MHz（欧洲）
  ::: info
  安装3DR遥测无线电时，请使用Pixhawk Mini附带的连接器，而非无线电设备自带的连接器。
  :::

- 3DR 10S Power Module
- WiFi Telemetry Radio
- Digital Airspeed Sensor

## 兼容性

### 遥控器

- PPM输出遥控接收机
- Spektrum DSM遥控接收机
- Futaba S BUS遥控接收机

### 电子调速器

- 所有标准PWM输入的电子调速器

## 连接器引脚分配（引脚输出）

![Pixhawk Mini - 连接器引脚分配](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_pinout.png)

## 产品比较

### Pixhawk Mini 与 Pixhawk（原始版）对比

- 尺寸缩小了三分之一 - 从 50x81.5x15.5mm 减少到 38x43x12mm。
- 采用 Rev 3 处理器，可充分利用 2MB 闪存。
- 改进的传感器，主IMU为MPU9250，次IMU为ICM20608。这将带来更稳定、更可靠的飞行和导航。
- 集成GPS+指南针模块。采用支持GLONASS的Neo M8N模块；指南针为HMC5983。可实现更快、更稳定的GPS定位。
- 采用Micro JST连接器，替代DF-13。这些连接器更易于操作。
- 集成压电扬声器和安全开关。
- 原生支持4S电池，并配备集成的PDB。

### Pixhawk Mini 与 Pixfalcon

- 改进的传感器，主惯性测量单元（IMU）为MPU9250，备用IMU为ICM20608。  
  可期待更好的振动处理和可靠性。  
- 支持UAVCAN的CAN接口。  
- 包含8通道舵机输出轨，适用于飞机及其他需要带电PWM输出的机体。  
- 包含I2C扩展板，共提供5个I2C连接。  
- 尺寸相近。  

Pixhawk Mini 配备来自ST Microelectronics®的先进处理器和传感器技术，以及NuttX实时操作系统，为控制任何自主机体提供出色的性能、灵活性和可靠性。

## 已知问题

- 某些Pixhawk Minis存在[硬件缺陷](https://github.com/PX4/PX4-Autopilot/issues/7327#issuecomment-317132917)，导致内部MPU9250 IMU不可靠。
  - 该问题仅存在于旧硬件版本中，因为[制造商已在此后修复了该问题](https://github.com/PX4/PX4-Autopilot/issues/7327#issuecomment-372393609)。
  - 要检查特定板是否受影响，请将板断开电源一段时间后重新上电，并尝试从PX4命令行启动mpu9250驱动。如果受影响，驱动将无法启动。
  - MPU9250在PX4固件中[默认禁用](https://github.com/PX4/PX4-Autopilot/pull/7618)。
  - 受损的Pixhawk Minis在没有外部磁力计或连接GPS的情况下（包括室内）都无法完成校准。
  - 当使用外部GPS时，[这不是问题](https://github.com/PX4/PX4-Autopilot/pull/7618#issuecomment-320270082)，因为ICM20608提供加速度计和陀螺仪数据，而外部GPS提供磁力计数据。

<a id="wiring"></a>

## 接线快速入门

:::warning
_Pixhawk Mini_ 已不再由3DR制造或销售。
:::

本快速入门指南介绍了如何为[Pixhawk Mini](../flight_controller/pixhawk_mini.md)供电并连接其最重要的外设。

### 标准接线图

下图展示了使用 _Pixhawk Mini 套件_ 和 3DR 通信无线电（包括 ESC、电机、电池以及运行在手机上的地面控制站）的标准 _四旋翼飞行器_ 接线方式。接下来的章节将逐一介绍各主要部件。

![Pixhawk Mini 电子接线图 QAV250（无框架）](../../assets/airframes/multicopter/lumenier_qav250_pixhawk_mini/qav250_wiring_image_pixhawk_mini.jpg)

::: info
对于其他类型的机体，输出接线/供电略有不同。下文将针对 VTOL、Plane、Copter 进行更详细的说明。
:::

### 安装和调整控制器方向

_Pixhawk Mini_ 应使用减震泡沫垫（包含在套件中）安装在机架上。应尽可能将其靠近机体的重心位置安装，保持顶面朝上且箭头指向机体前方。

![Pixhawk Mini 推荐方向](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_mounting_arrow.jpg)

![安装泡沫](../../assets/hardware/mounting/3dr_anti_vibration_mounting_foam.png)

::: info
如果控制器无法以推荐/默认方向安装（例如由于空间限制），您需要根据实际使用的方向配置自动驾驶仪软件：[Flight Controller Orientation](../config/flight_controller_orientation.md)
:::

### GPS + Compass

将3DR GPS + Compass通过提供的6针线缆连接到Pixhawk Mini的**GPS&I2C**端口（右上角）。  
GPS/Compass应尽可能远离其他电子设备安装，并朝向机体前方（将指南针与其他电子设备分开可减少干扰）。

![Connecting compass/GPS to Pixhawk Mini](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_with_compass.jpg)

NOTE - INSERT IMAGE SHOWING BOTH PORTS? OR FRONT-FACING image of GPS&I2C

指南针在首次使用前必须进行校准：[Compass Calibration](../config/compass.md)

### 电源

下图展示了在四旋翼飞行器中使用 _Pixhawk Mini_ 时的典型供电接线方式。  
通过套件中提供的 _Quad Power Distribution Board_ 电源分配板，可以从电池为 Pixhawk Mini 和电调/电机供电（还可为其他配件供电）。

::: info  
_Quad Power Distribution Board_ 配备的电源模块(PM)适用于<=4S电池。  
如果需要更高功率，推荐使用已停产的 _3DR 10S Power Module_ 电源模块。  
:::

![Pixhawk Mini - 供电](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_powering_quad_board.jpg)

_PIXhawk Mini_ 通过 **PM** 接口供电。  
当使用电源模块时（如本例），该接口还可读取模拟电压和电流测量值。

电源分配板最多可为4个电调单独供电（尽管本例中仅连接了一个）。

控制信号来自 MAIN OUT 接口。本例中仅有一个控制通道，通过 _8 Channel PWM Breakout Board_ PWM扩展板与电调连接。

Pixhawk Mini 的输出轨（MAIN OUT）无法为连接的设备供电（如图示电路无需供电）。  
对于 MAIN OUT 连接的需要主动供电的设备（如飞机舵机）的机体，需使用 BEC（电池消除电路）为输出轨供电。  
配套的扩展板允许一个通道为其他输出口提供电源。

### 无线电控制

Pixhawk Mini 支持多种无线电接收机型号：

- Spektrum 和 DSM 接收机连接到 **SPKT/DSM** 输入端口。

  <img src="../../assets/flight_controller/pixhawk_mini/pixhawk_mini_port_spkt_dsm.png" width="350px" title="Pixhawk Mini - Spektrum 接收机端口" />

- PPM-SUM 和 S.BUS 接收机连接到 **RCIN** 端口。

  <img src="../../assets/flight_controller/pixhawk_mini/pixhawk_mini_port_rcin.png" width="350px" title="Pixhaw Mini - PPM 接收机端口" />

- 使用 _每个通道独立导线_ 的 PPM 和 PWM 接收机必须通过 PPM 编码器 [比如这个](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html) 连接到 **RCIN** 端口（PPM-Sum 接收机为所有通道使用单根信号线）。

关于无线电系统选择、接收机兼容性及遥控器/接收机配对绑定的更多信息，请参阅：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)

### 安全开关（可选）

控制器集成了一个安全开关，当自动驾驶仪准备好起飞时，可以使用该开关来激活电机。
如果在特定机体上该开关难以触及，可以附加（可选）外部安全按钮，如下图所示。

![Pixhawk Mini - Optional Switch](../../assets/flight_controller/pixhawk_mini/pixhawk_mini_safety_switch_wiring.jpg)

### 遥测无线电

### 电机

所有支持的空中和地面机型的MAIN/AUX输出端口与电机/舵机之间的映射关系，详见[Airframe Reference](../airframes/airframe_reference.md)。

:::警告
不同机型的映射关系不一致（例如，不能依赖油门信号始终在相同输出口）。
请确保为您的机体使用正确的映射关系。
:::

:::提示
如果您的机型未在参考文档中列出，请使用相应类型的"generic"通用机型。
::: 信息：

- 输出通道必须单独供电，如上文[Power](#电源)部分所述。
- Pixhawk Mini 不能用于QuadPlane VTOL机型。这是因为QuadPlane需要9个输出（4个Main，5个AUX），而Pixhawk Mini仅有8个输出（8个Main）。

<img src="../../assets/flight_controller/pixhawk_mini/pixhawk_mini_port_main_out.png" width="350px" title="Pixhawk Mini - 电机/舵机端口" />

### 其他外设

其他组件的接线和配置方式将在[外设](../peripherals/index.md)相关专题中分别说明。

### 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 特定配置请参考：[QuadPlane VTOL配置](../config_vtol/vtol_quad_configuration.md)

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接了合适的硬件时，它会由_QGroundControl_预先构建并自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)针对此目标：

```
make px4_fmu-v3_default
```

## 调试端口

该板没有调试端口（即没有访问 [系统控制台](../debug/system_console.md) 或 [SWD 接口](../debug/swd_debug.md) 的端口）。

开发者需要将导线焊接到板上的测试垫以获取 SWD 接口，并焊接到 STM32F4 (IC) 的 TX 和 RX 引脚以获取控制台。