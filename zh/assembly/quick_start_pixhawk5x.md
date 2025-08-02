# Holybro Pixhawk 5x 接线快速入门

:::warning
PX4 并不制造此款（或任何）自动驾驶仪。
硬件支持或合规性问题请联系[制造商](https://holybro.com/)
:::

本快速入门指南展示如何为 [Pixhawk<sup>&reg;</sup> 5X](../flight_controller/pixhawk5x.md) 飞行控制器供电并连接其最重要的外接设备。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_standard_set.jpg" width="520px" title="Pixhawk5x 标准套装" />

Pixhawk 5 标准套装

## 接线图概览

下图展示了如何连接最重要的传感器和外设。

![Pixhawk 5x Wiring Overview](../../assets/flight_controller/pixhawk5x/pixhawk5x_wiring_diagram.jpg)

:::tip
有关可用端口的更多信息，请参见此处：[Pixhawk 5X > Connections](../flight_controller/pixhawk5x.md#connections)。
:::

## 安装和调整控制器

_Pixhawk 5X_ 可以使用套件中包含的双面胶带安装在框架上。  
应尽可能将其靠近机体的重心位置安装，顶部朝上，箭头指向机体前方。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_vehicle_front1.jpg" width="400px" title="Pixhawk5x standard set" />

::: info  
如果控制器无法以推荐/默认方向安装（例如由于空间限制），您需要根据实际使用的方向配置自动驾驶仪软件：[飞行控制器方向](../config/flight_controller_orientation.md)。  
:::

## GPS + 指南针 + 蜂鸣器 + 安全开关 + LED

_PIXhawk5X Standard Set_ 可搭配 M8N 或 M9N GPS（10 针接口）购买，应连接至 **GPS1** 接口。
这些 GNSS 模块集成了指南针、安全开关、蜂鸣器和 LED。

一个 [M8N 或 M9N GPS](https://holybro.com/collections/gps)（6 针接口）可单独购买并连接至 **GPS2** 接口。

GPS/指南针应 [安装在机体框架上](../assembly/mount_gps_compass.md)，尽可能远离其他电子设备，且方向标记朝向机体前方（将指南针与其他电子设备分离可减少干扰）。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_gps_front.jpg" width="200px" title="Pixhawk5x standard set" />

::: info
GPS 模块集成的安全开关默认启用（启用时，PX4 将不允许你解锁机体）。
要禁用安全开关，请按住安全开关 1 秒。
再次按下安全开关可启用安全功能并解除机体锁定（如果由于任何原因无法通过遥控器或地面站解除机体锁定，此功能非常有用）。
:::

## 电源

使用6线电缆将Standard Set中提供的_PM02D电源模块_(PM板)的输出连接到_Pixhawk 5X_的**POWER**端口之一。Pixhawk 5X的PM02D和电源端口使用[2.00mm Pitch CLIK-Mate Wire-to-Board PCB Receptacle](https://www.molex.com/molex/products/part-detail/pcb_receptacles/5024430670)及[Housing](https://www.molex.com/molex/products/part-detail/crimp_housings/5024390600)的6路电路。

PM02D电源模块支持**2~6S**电池，板输入应连接到您的锂聚合物电池。注意PM板不会向**FMU PWM OUT**和**I/O PWM OUT**的+/-引脚供电。

如果使用固定翼或地面车辆，**FMU PWM-OUT**需要单独供电以驱动舵机（如方向舵、升降舵等）。这可以通过将**FMU PWM-OUT**的8针电源（+）轨连接到电压调节器来实现（例如，配备BEC的电调、独立5V BEC或2S锂聚合物电池）。

::: info
电源轨电压必须与所使用的舵机匹配！
:::

| 引脚与连接器 | 功能 |
| ------------ | ---- |
| I/O PWM Out  | 在此处连接电机信号线和GND线 |
| FMU PWM Out  | 在此处连接舵机信号线、正电源线和GND线 |

::: info
PX4固件中的**MAIN**输出对应_Pixhawk 5X_的**I/O PWM OUT**端口，而**AUX输出**对应**FMU PWM OUT**端口。例如，**MAIN1**对应**I/O PWM OUT**的IO_CH1引脚，**AUX1**对应**FMU PWM OUT**的FMU_CH1引脚。
:::

_Pixhawk 5X_电源端口的引脚分配如下所示。电源端口通过PM02D电源模块接收I2C数字信号以获取电压和电流数据。VCC线路需要提供至少3A的持续电流，默认电压应为5.2V。5V较低电压仍可接受，但不推荐。

| 引脚 | 信号 | 电压 |
| ---- | ---- | ---- |
| 1(红色) | VCC | +5V |
| 2(黑色) | VCC | +5V |
| 3(黑色) | SCL | +3.3V |
| 4(黑色) | SDA | +3.3V |
| 5(黑色) | GND | GND |
| 6(黑色) | GND | GND |

## 遥控控制

如果要_手动_控制机体(PX4在自主飞行模式下不需要遥控系统)，则必须使用遥控(RC)无线电系统。

你需要[选择兼容的发射机/接收机](../getting_started/rc_transmitter_receiver.md)，然后将它们_绑定_以实现通信(阅读随附的发射机/接收机说明书)。

- Spektrum/DSM接收机连接到**DSM/SBUS RC**输入。
- PPM或SBUS接收机连接到**RC IN**输入端口。

具有_每个通道单独连线_的PPM和PWM接收机必须通过PPM编码器[如本产品](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)连接到**RC IN**端口(PPM-Sum接收机通过单根信号线传输所有通道)。

关于如何选择无线电系统、接收机兼容性以及如何绑定发射机/接收机对的更多信息，请参阅：[遥控发射机与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

[遥测无线电](../telemetry/index.md)可用于从地面站对机体进行通信和飞行控制（例如，您可以指挥无人机飞往特定位置，或上传新的任务）。

机体端的无线电应连接到**TELEM1**端口（如连接到该端口，无需进一步配置）。
另一块无线电连接到您的地面站计算机或移动设备（通常通过USB连接）。

您还可以在[Holybro的网站](https://holybro.com/collections/telemetry-radios)购买相关无线电设备。

## SD卡（可选）

强烈建议使用SD卡，因为它们用于[记录和分析飞行细节](../getting_started/flight_reporting.md)，运行任务，以及使用UAVCAN总线硬件。  
将卡片（包含在Pixhawk 5X套件中）插入_Pixhawk 5X_中，如下图所示。

<img src="../../assets/flight_controller/pixhawk5x/pixhawk5x_sd_slot.jpg" width="420px" title="Pixhawk5x standard set" />

:::tip  
欲了解更多信息，请参阅[基础概念 > SD卡（可移动存储）](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)。  
:::

## 电机

电机/舵机按照机体在[Airframe Reference](../airframes/airframe_reference.md)中指定的顺序连接到 **I/O PWM OUT** (**MAIN**) 和 **FMU PWM OUT** (**AUX**) 端口。

::: info
此参考文档列出了所有支持的飞行器和地面框架的输出端口到电机/舵机的映射关系（如果您的框架未在参考文档中列出，则使用相应类型的"generic"飞行器）。
:::

:::warning
不同框架的映射关系不一致（例如，不能依赖推力通道在所有飞机框架中始终位于同一输出端口）。请确保为您的机体使用正确的映射关系。
:::

## 其他外设

可选或不常见组件的接线和配置在相关主题的[外设](../peripherals/index.md)中进行了介绍。

## 接线图

![Pixhawk 5X 接线图1](../../assets/flight_controller/pixhawk5x/pixhawk5x_pinout.png)

您也可以从[这里](https://github.com/PX4/PX4-user_guide/blob/main/assets/flight_controller/pixhawk5x/pixhawk5x_pinout.pdf)或[这里](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Pinout.pdf)下载 _Pixhawk 5X_ 的接线图。

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

QuadPlane 特定配置请参考：[QuadPlane 垂直起降配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->

## 进一步信息

- [Pixhawk 5X](../flight_controller/pixhawk5x.md) (PX4 文档概览页面)
- [Pixhawk 5X 概述与规格](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Spec_Overview.pdf) (Holybro)
- [Pixhawk 5X 引脚分配](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pixhawk5X_Pinout.pdf) (Holybro)
- [FMUv5X 参考设计引脚分配](https://docs.google.com/spreadsheets/d/1Su7u8PHp-Y1AlLGVuH_I8ewkEEXt_bHHYBHglRuVH7E/edit#gid=562580340).
- [Pixhawk 飞控FMUv5X 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-011%20Pixhawk%20Autopilot%20v5X%20Standard.pdf).
- [Pixhawk 飞控总线标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-010%20Pixhawk%20Autopilot%20Bus%20Standard.pdf).
- [Pixhawk 接口标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf).