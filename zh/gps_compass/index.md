# 全球导航卫星系统 (GNSS)

GNSS 系统对于任务执行、部分自动模式以及手动/辅助模式是必需的。  
PX4 支持通过 u-blox、MTK Ashtech 或 Emlid 协议，或通过 UAVCAN 通信的接收器，使用 GPS、GLONASS、Galileo、北斗、QZSS 和 SBAS 等全球导航卫星系统 (GNSS)。

最多可通过 UART 或 CAN 总线连接两个 GPS 模块：

- 一个主 [GNSS 模块](../gps_compass/#supported-gnss)，通常还包括 [指南针/磁力计](../gps_compass/magnetometer.md)、[蜂鸣器](../getting_started/px4_basic_concepts.md#buzzer)、[安全开关](../getting_started/px4_basic_concepts.md#safety-switch) 和 [UI LED](../getting_started/led_meanings.md#ui-led)。
- 一个可选的备用 GNSS/指南针模块，用于主模块失效时的回退。  
  该模块可能包含蜂鸣器、安全开关和 LED，但 PX4 不会使用这些功能。

![GPS + Compass](../../assets/hardware/gps/gps_compass.jpg)

::: info
PX4 同样支持 [实时动态定位 (RTK)](../gps_compass/rtk_gps.md) 和 **后处理动态定位 (PPK)** 接收器，这些可将 GNSS 系统的精度扩展至厘米级别。
:::

## 支持的GNSS

PX4应能与通过u-blox、MTK Ashtech或Emlid协议通信的任何单元配合使用，或通过UAVCAN通信的单元。

此表格包含非RTK GNSS单元（其中大部分还带有指南针）。这些设备已由PX4开发团队测试，或在PX4社区内较为流行。

| 设备                                                       |     GPS     |          指南针          | [CAN](../dronecan/index.md) | 蜂鸣器 / 安全开关 / LED | 说明                       |
| :----------------------------------------------------------- | :---------: | :-----------------------: | :-------------------------: | :-------------------: | :-------------------------- |
| [ARK GPS](../dronecan/ark_gps.md)                            |     M9N     |          BMM150           |              ✓              |           ✓           | + 气压计、IMU                 |
| [ARK SAM GPS](../gps_compass/ark_sam_gps.md)                 |  SAM-M10Q   |          IIS2MDC          |                             |           ✓           |                             |
| [ARK TESEO GPS](../dronecan/ark_teseo_gps.md)                | Teseo-LIV4F |          BMM150           |              ✓              |           ✓           | + 气压计、IMU                 |
| [Avionics Anonymous UAVCAN GNSS/Mag][avionics_anon_can_gnss] |   SAM-M8Q   |         MMC5983MA         |              ✓              |           ✘           |                             |
| [CUAV NEO 3 GPS](../gps_compass/gps_cuav_neo_3.md)           |     M9N     |          IST8310          |                             |           ✓           |                             |
| [CUAV NEO 3 Pro GPS](../gps_compass/gps_cuav_neo_3pro.md)    |     M9N     |          RM3100           |              ✓              |           ✓           | + 气压计                      |
| [CUAV NEO 3X GPS](../gps_compass/gps_cuav_neo_3x.md)         |     M9N     |          RM3100           |              ✓              |          ✘✓✓          | + 气压计。                    |
| [CubePilot Here2 GNSS GPS (M8N)][CubePilot Here2]            |     M8N     |         ICM20948          |                             |           ✓           | 已被 HERE3 取代。          |
| [Emlid Reach M+](https://emlid.com/reach/)                   |      ✓      |             ✘             |                             |           ✘           | 支持PPK。预计支持RTK。       |
| [Holybro DroneCAN M8N GPS](../dronecan/holybro_m8n_gps.md)   |     M8N     |          BMM150           |              ✓              |           ✘           | + 气压计                      |
| [Holybro Micro M8N GPS][Hb Micro M8N]                        |     M8N     |          IST8310          |                             |           ✘           |                             |
| [Holybro Nano Ublox M8 5883 GPS][hb_nano_m8_5883]            |  UBX-M8030  |          QMC5883          |                             |           ✘           |                             |
| [Holybro M8N GPS](../gps_compass/gps_holybro_m8n_m9n.md)     |     M8N     |          IST8310          |                             |           ✓           |                             |
| [Holybro M9N GPS](../gps_compass/gps_holybro_m8n_m9n.md)     |     M9N     |          IST8310          |                             |           ✓           |                             |
| [Holybro DroneCAN M9N GPS][hb_can_m9n]                       |     M9N     |          BMM150           |              ✓              |           ✓           |                             |
| [Holybro M10 GPS][hb_m10]                                    |     M10     |          IST8310          |                             |           ✓           |                             |
| [Hobbyking u-blox Neo-M8N GPS & Compass][hk_ublox_neo_8mn]   |     M8N     |             ✓             |                             |           ✘           |                             |
| [LOCOSYS Hawk A1 GNSS receiver][LOCOSYS Hawk A1]             | MC-1612-V2b |         optional          |                             |          ✘✘✓          |                             |
| [LOCOSYS Hawk R1](../gps_compass/rtk_gps_locosys_r1.md)      | MC-1612-V2b |                           |                             |          ✘✘✓          |                             |
| [LOCOSYS Hawk R2](../gps_compass/rtk_gps_locosys_r2.md)      | MC-1612-V2b |          IST8310          |                             |          ✘✘✓          |                             |
| [mRo GPS u-blox Neo-M8N Dual Compass][mro_neo8mn_dual_mag]   |     M8N     |     LIS3MDL, IST8308      |                             |           ✘           |                             |
| [RaccoonLab L1 GNSS NEO-M8N][RccnLabGNSS250]                 |   NEO-M8N   |          RM3100           |              ✓              |          ✘✘✓          | + 气压计                      |
| [Sky-Drones SmartAP GPS](../gps_compass/gps_smartap.md)      |     M8N     | HMC5983, IST8310, LIS3MDL |                             |           ✓           | + 气压计                      |
| [Zubax GNSS 2](https://zubax.com/products/gnss_2)            |   MAX-M8Q   |          LIS3MDL          |                             |           ✘           | + 气压计                      |

<!-- 用于优化表格编辑布局的链接 -->

[avionics_anon_can_gnss]: https://www.tindie.com/products/avionicsanonymous/uavcan-gps-magnetometer/
[hk_ublox_neo_8mn]: https://hobbyking.com/en_us/ublox-neo-m8n-gps-with-compass.html
[mro_neo8mn_dual_mag]: https://store.mrobotics.io/product-p/m10034-8308.htm
[hb_nano_m8_5883]: https://holybro.com/products/nano-m8-5883-gps
[hb_can_m9n]: https://holybro.com/products/dronecan-m9n-gps
[hb_m10]: https://holybro.com/products/m10-gps
[LOCOSYS Hawk A1]: https://www.locosys.com.tw/product/hawk-a1/
[RccnLabGNSS250]: https://raccoon-lab.com/products/gnss-250
[CubePilot Here2]: https://www.cubepilot.org/product/here2-gps
[Hb Micro M8N]: https://holybro.com/products/micro-m8n-gps

注：
- ✓ 或具体型号表示该功能受支持
- ✘ 表示该功能不支持
- SafeSw 是专有安全开关技术的简称（保留英文）## GNSS/指南针的安装

大多数GNSS模块都包含[指南针/磁力计](../gps_compass/magnetometer.md)部分（详见链接中的校准/设置信息）。  
由于这个原因，GNSS模块应尽可能远离电机/电调电源线安装——通常安装在支架或机翼上（固定翼飞机）。

[Mounting the Compass](../assembly/mount_gps_compass.md) 解释了如何安装带有指南针的GNSS模块。

## 硬件设置

硬件设置取决于飞行控制器、GNSS模块及其支持的连接总线 - UART/I2C 或 CAN。

### Pixhawk 标准连接器

当使用支持 [Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 的飞行控制器时，GNSS/磁力计模块的连接最为简便。  
所有遵循该标准的飞行控制器（包括大多数 [Pixhawk Standard](../flight_controller/autopilot_pixhawk_standard.md) 控制器及许多其他型号）均采用相同的端口连接器和布线方式连接GNSS模块。  
由于这一标准化设计，许多流行的GNSS/磁力计模块可直接插入飞行控制器的对应端口。

若使用通过通用UART和串口协议（如I2C）连接的GNSS/磁力计模块：

- 主GNSS/磁力计模块应连接到标有 `GPS1`、`GPS&SAFETY` 或 `GPS` 的10针端口（标准中描述为"Full GPS + Safety Switch Port"）。  
  GPS应包含 [蜂鸣器](../getting_started/px4_basic_concepts.md#buzzer)、[安全开关](../getting_started/px4_basic_concepts.md#safety-switch) 和 [用户界面LED](../getting_started/led_meanings.md#ui-led)。
- 若存在（可选的）备用模块，可连接到标有 `GPS2` 的6针端口（标准中为"Basic GPS Port"）。
- 这些端口通常仅对u-blox模块即插即用。

::: info
端口包含一个GNSS用UART和一个磁力计用I2C端口。  
"Full GPS + Safety Switch Port" 包含额外的I2C连接器用于LED、蜂鸣器和安全开关。  
您完全可以通过其他空闲UART将GPS引脚作为GNSS端口，或将磁力计或蜂鸣器连接到I2C端口。  
但若这样做则需要 [配置端口](../peripherals/serial_configuration.md)。
:::

对于 [DroneCAN](../dronecan/index.md#supported-hardware) GNSS/磁力计模块：

- DroneCan GPS模块连接到标有 `CAN1` 或 `CAN2` 的4针CAN总线端口。

### 其他飞行控制器/GNSS模块

若您使用的飞行控制器与GNSS模块组合不符合Pixhawk连接器标准，则需要特别注意飞行控制器和模块的连接器引脚定义。  
可能需要重新布线或焊接连接器。

:::warning
某些飞行控制器的端口可能具有软件兼容性但无法通过连接器兼容（即使使用相同连接器！），这是由于它们的引脚顺序不同导致的。
:::

连接器标准的引脚定义在标准文档中均有说明。  
其他控制器和GNSS模块的引脚定义应包含在其制造商文档中。

## GNSS配置

通过GPS串口连接的GPS模块的默认配置如下所示。  
PX4或制造商设备文档中可能提供额外的设备特定配置（例如：[Trimble MB-Two > 配置](../gps_compass/rtk_gps_trimble_mb_two.md#configuration)）。

### 主GPS配置（UART）

Pixhawk上的主GPS配置对U-Blox GPS模块是透明的——只需将GPS模块连接到标记为 `GPS1`、`GPS&SAFETY` 或 `GPS`（若只有一个GPS端口）的端口，一切功能都应正常工作。

默认的 [串口配置](../peripherals/serial_configuration.md#default_port_mapping) 通过 [GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG) 将 `GPS1` 配置为GPS端口，使用 [GPS_1_PROTOCOL](../advanced_config/parameter_reference.md#GPS_1_PROTOCOL) 将协议设置为 `u-blox`，并通过 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) 将波特率设置为 `0: Auto`。

对于Trimble、Emlid、MTK等类型的GPS，需要相应修改 `GPS_1_PROTOCOL`。  
对于 _Trimble MB-Two_，还需要修改 `SER_GPS1_BAUD` 以将波特率设置为115200。

<a id="dual_gps"></a>

### 从GPS配置（UART）

要使用从GPS，通常将其连接到名为 `GPS2` 的端口（如果存在），否则连接到任意空闲UART端口。  
该端口可能已预配置，但与主端口不同，这并不保证。

为确保端口配置正确，需进行 [串口配置](../peripherals/serial_configuration.md) 以将 [GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG) 分配给所选端口。

以下步骤展示如何在 _QGroundControl_ 中配置 `GPS 2` 端口的从GPS：

1. [查找并设置](../advanced_config/parameters.md) 参数 [GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG) 为 **GPS 2**。

   - 打开 _QGroundControl_ 并导航到 **机体设置 > 参数** 部分。
   - 选择 **GPS** 标签页，然后打开 [GPS_2_CONFIG](../advanced_cofnfig/parameter_reference.md#GPS_2_CONFIG) 参数，从下拉列表中选择 `GPS 2`。

     ![QGC串口示例](../../assets/peripherals/qgc_serial_config_example.png)

1. 重启机体以显示其他参数。
1. 选择 **串口** 标签页，打开 [SER_GPS2_BAUD](../advanced_config/parameter_reference.md#SER_GPS2_BAUD) 参数（`GPS 2` 端口波特率）：将其设置为 _Auto_（Trimble设备则设置为115200）。

   ![QGC串口波特率示例](../../assets/peripherals/qgc_serial_baudrate_example.png)

配置第二GPS端口后：

1. 配置ECL/EKF2估计器以融合两个GPS系统的数据。  
   详细说明请参见：[使用ECL EKF > 双接收机](../advanced_config/tuning_the_ecl_ekf.md#dual-receivers)。

### DroneCAN GNSS配置

[DroneCAN](../dronecan/index.md#supported-hardware) GNSS配置在链接文档中有所介绍（以及在特定模块的文档中）。

### 配置GPS作为偏航/航向源

当使用支持 _设备偏航输出_ 的模块时，GPS可作为偏航融合的来源。  
此内容在[RTK GPS > 配置GPS作为偏航/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source)中有详细说明。

## 指南针配置

内置指南针部件的指南针校准在以下链接中有介绍：[指南针配置](../config/compass.md)。

## GNSS数据概述

PX4使用了大多数GNSS模块能够提供的信息子集。这些数据被写入[uORB消息SensorGps](../msg_docs/SensorGps.md)，并作为估计器的输入用于全局位置估计。同时，这些数据也通过MAVLink消息流传输，例如[GPS_RAW_INT](https://mavlink.io/en/messages/common.html#GPS_RAW_INT)和[GPS2_RAW](https://mavlink.io/en/messages/common.html#GPS2_RAW)。

以下是一些有助于解读GNSS数据的术语：

- `DOP`：位置误差放大因子（无量纲）。该参数反映了卫星几何分布对GPS接收器计算精度的影响程度。
- `EPH`：水平位置误差标准差（米）。该参数表示GPS定位的纬度和经度不确定度。
- `EPV`：垂直位置误差标准差（米）。该参数表示GPS定位海拔高度的不确定度。

### DOP与EPH/EPV对比

DOP是基于卫星位置对高精度潜力的衡量。而EPH/EPV更为全面：它们直接估计GPS位置误差，综合考虑了卫星几何分布以及其他误差源（如信号噪声和大气效应）。在存在显著信号噪声或大气干扰时，即使DOP值较低（卫星几何分布良好），EPH/EPV值仍可能较高。

因此，EPH/EPV值能够更直接且实际地反映在当前条件下可预期的GPS实际精度。

## 开发者信息

- GPS/RTK-GPS
  - [RTK-GPS](../advanced/rtk_gps.md)
  - [GPS驱动](../modules/modules_driver.md#gps)
  - [DroneCAN 示例](../dronecan/index.md)
- 指南针
  - [驱动源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer) (指南针)