# Pix32 v5 接线快速入门

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://holybro.com/)。
:::

本快速入门指南展示如何为 [Holybro Pix32v5](../flight_controller/holybro_pix32_v5.md)<sup>&reg;</sup> 飞行控制器供电并连接其最重要的外设。

![Pix32 v5 With Base](../../assets/flight_controller/holybro_pix32_v5/IMG_3165.jpg)

## 开箱

Pix32 v5 与多种配件组合一起销售，包括 _pix32 v5 主板_、电源模块 _PM02 V3_ 以及 [Holybro M8N GPS](https://holybro.com/collections/gps/products/m8n-gps) (UBLOX NEO-M8N)。

包含 _PM02 V3_ 电源模块和 _Pixhawk 4 GPS/Compass_ 的包装内容如下所示。
包装盒内还包括接线图指南、电源模块说明书和主板（下图未显示）。

![Pix32 v5 包装盒](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_unboxing_schematics.png)

## 接线图概述

下图展示了重要传感器和外设（除电机和舵机输出外）的连接方式。后续章节将对这些内容进行详细说明。

![Pix32 v5 接线图概述](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_wiring_overview.jpg)

:::tip
更多关于可用端口的信息可以在这里找到[Holybro_Pix32-V5-Base-Mini-Pinouts.pdf](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf)。
:::

## 安装与调整控制器

_Pix32 v5_ 应安装在机架上，尽可能靠近机体的重心位置，方向应顶部朝上，箭头指向机体前方。

![Pix32 v5 With Orientation](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_orientation.png)

::: info
如果控制器无法以推荐/默认方向安装（例如由于空间限制），则需要在自动驾驶仪软件中配置实际使用的方向：[Flight Controller Orientation](../config/flight_controller_orientation.md)。
:::

:::tip
该电路板具有内部减震装置。
无需使用减震泡沫固定控制器（双面胶通常已足够）。
:::

## GPS + 电子罗盘 + 蜂鸣器 + 安全开关 + LED

Pix32 v5 设计用于与 [Holybro M8N GPS](https://holybro.com/collections/gps/products/m8n-gps) 配合使用，该设备集成了电子罗盘、安全开关、蜂鸣器和 LED。
它通过 10 针线缆直接连接到 **GPS 接口**。

![Pix32 v5 与 GPS 连接](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_connection_gps_compass.jpg)

GPS/电子罗盘应尽量远离其他电子设备安装，方向标记朝向机体前方（将电子罗盘与其他电子设备分离可减少干扰）。

::: info
GPS 模块集成的安全开关 _默认启用_（启用时，PX4 将不允许解锁机体）。
要禁用安全开关，请按住安全开关 1 秒。
您可以再次按下安全开关以启用安全功能并禁用机体（当您无法通过遥控器或地面站解除机体锁定时，此功能非常有用）。
:::

## Power

你可以使用电源模块或电源分配板为电机/舵机供电并测量功耗。
推荐的电源模块如下所示。

<a id="pm02_v3"></a>

### PM02 v3 电源模块

[Power Module (PM02 v3)](../power_module/holybro_pm02.md) 可以与 _pix32 v5_ 配套使用。
它为飞控提供稳压电源，并将电池电压/电流传输至飞控。

请按以下方式连接电源模块的输出。

![Pix32 v5 With Power Module](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_connection_power.jpg)

- PM 电压/电流端口：使用配套的6芯GH线缆连接至 POWER1 端口（或 `POWER2`）。
- PM 输入（XT60公头）：连接至 LiPo 电池（2~12S）。
- PM 电源输出（XT60母头）：连接至任一电机电调。

::: info
由于该电源模块不含电源分配线路，通常需将所有电调并联至电源模块输出端（电调需适配输入电压等级）。
:::

::: info
**MAIN/AUX** 的8针电源（+）总线不会由电源模块为飞控供电。
若需单独供电以驱动舵机（如方向舵、升降舵等），需将电源总线连接至带BEC的电调、独立5V BEC或2S LiPo电池。
请确保所用舵机的电压适配。
:::

电源模块具有以下特性/限制：

- 最大输入电压：60V
- 最大电流测量：120A 电压
- 电流测量配置为 SV ADC 开关稳压器输出 5.2V 和 3A 最大
- 重量：20g
- 包装包含：
  - PM02 板
  - 6针 MLX 线缆（1根）
  - 6针 GH 线缆（1根）

### 电池配置

电池/电源设置必须在 [Battery Estimation Tuning](../config/battery.md) 中配置。
对于任一电源模块，都需要配置 _电池节数_。

除非使用其他电源模块（如Pixracer的电源模块），否则无需更新 _电压分压器_。

## 遥控

如果你想要_手动_控制你的机体（PX4在自主飞行模式下不需要遥控系统），则需要使用遥控系统。

你需要[选择兼容的遥控器/接收机](../getting_started/rc_transmitter_receiver.md)，然后_绑定_它们以实现通信（阅读随附的特定遥控器/接收机的说明书）。

以下说明展示如何将不同类型的接收机连接到带底板的_Pix32 v5_：

- Spektrum/DSM接收机连接到下图所示的**DSM RC**输入端口。

  ![Pix32v5 遥控接收机](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_receivers_connection.jpg)

- PPM和S.Bus接收机连接到**SBUS_IN/PPM_IN**输入端口（标记为RC IN）：

  ![引脚定义](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_pinouts_back_label.png)

- 采用_每个通道单独线缆_的PPM和PWM接收机必须通过PPM编码器连接到**PPM RC**端口[例如这个](http://www.getfpv.com/radios/radio-accessories/holybro-ppm-encoder-module.html)（PPM-Sum接收机使用单根信号线传输所有通道）。

更多关于选择遥控系统、接收机兼容性以及绑定遥控器/接收机对的信息，请参阅：[遥控器与接收机](../getting_started/rc_transmitter_receiver.md)。

## 遥测无线电（可选）

遥测无线电可用于从地面站与飞行中的机体进行通信和控制（例如，您可以引导无人飞行器到达特定位置，或上传新任务）。

机体上的无线电应连接到 **TELEM1** 端口（如连接到此端口，则无需进一步配置）。
另一台无线电连接到您的地面站计算机或移动设备（通常通过 USB 连接）。

![Pix32 v5 With Telemetry Radios](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_telemetry_radio.jpg)

## SD卡（可选）

SD卡主要用于[记录和分析飞行细节](../getting_started/flight_reporting.md)。  
pix32 v5 应预装了 micro SD 卡，如果您有自备的 micro SD 卡，请按照下图所示将其插入 _pix32 v5_。

![Pix32 v5 With SD Card](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_sd_card.jpg)

:::tip  
SanDisk Extreme U3 32GB [强烈推荐](../dev_log/logging.md#sd-cards)。  
:::

## 电机

电机/舵机的控制信号按照你机体在[机体参考](../airframes/airframe_reference.md)中指定的顺序连接到 **I/O PWM OUT** (**MAIN**) 和 **FMU PWM OUT** (**AUX**) 接口。

![Pix32 v5 - 背部引脚图（原理图）](../../assets/flight_controller/holybro_pix32_v5/pix32_v5_pinouts_back_label.png)

电机必须[单独供电](#Power)。

::: info
如果您的机架未列在机体参考中，则使用正确类型的"通用"机体。
:::

## 其他外设

非必需/较少见组件的接线和配置包含在各外设的专题中。[外设](../peripherals/index.md)

## 引脚分配

[Pix32 v5 引脚分配](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf) (Holybro)

## 配置

通用配置信息请参考：[自动驾驶仪配置](../config/index.md)

四旋翼飞行器专用配置请参考此处：[QuadPlane VTOL配置](../config_vtol/vtol_quad_configuration.md)

<!-- Nice to have detailed wiring infographic and instructions for different vehicle types. -->

## 更多信息

- [Pix32 v5 概述](../flight_controller/holybro_pix32_v5.md) (概述页面)
- [Pix32 v5 技术数据表](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5_technical_data_sheet_v1.1.pdf)
- [Pix32 v5 引脚图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_Pix32-V5-Base-Mini-Pinouts.pdf)
- [Pix32 v5 底板原理图](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_PIX32-V5-BASE-Schematic_diagram.pdf)
- [Pix32 v5 底板组件布局](https://holybro.com/manual/Holybro_PIX32-V5-BASE-ComponentsLayout.pdf)
- [FMUv5 参考设计引脚图](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)