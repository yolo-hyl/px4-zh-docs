

# 全球导航卫星系统 (GNSS)

GNSS系统对于任务执行以及某些自动和手动/辅助模式是必需的。  
PX4支持通过u-blox、MTK Ashtech或Emlid协议，或通过UAVCAN通信的接收器使用的全球导航卫星系统（GNSS），包括GPS、GLONASS、Galileo、Beidou、QZSS和SBAS等。  

最多可通过UART或CAN总线连接两个GPS模块：  

- 一个主[GNSS模块](../gps_compass/#supported-gnss)，通常同时包含[指南针/磁力计](../gps_compass/magnetometer.md)、[蜂鸣器](../getting_started/px4_basic_concepts.md#buzzer)、[安全开关](../getting_started/px4_basic_concepts.md#safety-switch)和[用户界面LED](../getting_started/led_meanings.md#ui-led)。  
- 一个可选的备用GNSS/指南针模块，用于主模块失效时的回退。  
  可能包含蜂鸣器、安全开关、LED，但PX4不会使用这些功能。  

![GPS + Compass](../../assets/hardware/gps/gps_compass.jpg)  

::: info  
PX4也支持[实时动态（RTK）](../gps_compass/rtk_gps.md)和**后处理动态（PPK）** GNSS接收器，这些技术可将GNSS系统精度提升至厘米级。  
:::

## 支持的GNSS

PX4应与通过u-blox、MTK Ashtech或Emlid协议，或通过UAVCAN通信的任何设备兼容。

此表包含非RTK GNSS设备（其中大多数也包含指南针）。
这些设备已由PX4开发团队测试，或在PX4社区中较为流行。

| 设备                                                       |     GPS     |          Compass          | [控制器局域网](../dronecan/index.md) | 蜂鸣器 / SafeSw / LED | 备注                       |
| :----------------------------------------------------------- | :---------: | :-----------------------: | :-------------------------: | :-------------------: | :-------------------------- |
| [ARK GPS](../dronecan/ark_gps.md)                            |     M9N     |          BMM150           |              ✓              |           ✓           | + 气压计, IMU                 |
| [ARK SAM GPS](../gps_compass/ark_sam_gps.md)                 |  SAM-M10Q   |          IIS2MDC          |                             |           ✓           |                             |
| [ARK TESEO GPS](../dronecan/ark_teseo_gps.md)                | Teseo-LIV4F |          BMM150           |              ✓              |           ✓           | + 气压计, IMU                 |
| [Avionics Anonymous UAVCAN GNSS/Mag][avionics_anon_can_gnss] |   SAM-M8Q   |         MMC5983MA         |              ✓              |           ✘           |                             |
| [CUAV NEO 3 GPS](../gps_compass/gps_cuav_neo_3.md)           |     M9N     |          IST8310          |                             |           ✓           |                             |
| [CUAV NEO 3 Pro GPS](../gps_compass/gps_cuav_neo_3pro.md)    |     M9N     |          RM3100           |              ✓              |           ✓           | + 气压计                      |
| [CUAV NEO 3X GPS](../gps_compass/gps_cuav_neo_3x.md)         |     M9N     |          RM3100           |              ✓              |          ✘✓✓          | + 气压计                     |
| [CubePilot Here2 GNSS GPS (M8N)][CubePilot Here2]            |     M8N     |         ICM20948          |                             |           ✓           | 已被HERE3取代         |
| [Emlid Reach M+](https://emlid.com/reach/)                   |      ✓      |             ✘             |                             |           ✘           |                             |
| [Holy Skyworks UAVCAN GPS](../holy-skyworks-uavcan-gps.md) |      ✓      |          ✓               |              ✓              |           ✘           |                             |

注：
- ✓ 或具体部件编号表示功能已支持，✘ 或空白表示功能未支持
- 部分型号使用了部件名称而非缩写
- 列表包含部分已停产设备（如Here GPS）

[avionics_anon_can_gnss]: https://www.tindie.com/products/avionicsanonymous/uavcan-gps-magnetometer/
[CubePilot Here2]: https://www.cubepilot.org/here2-gnss/

## 安装GNSS/指南针

大多数GNSS模块还包含一个[指南针/磁力计](../gps_compass/magnetometer.md)部分（详见链接中的校准/设置信息）。  
因此，GNSS模块应尽可能远离电机/ESC电源线安装——通常安装在支架或机翼上（适用于固定翼）。

[安装指南针](../assembly/mount_gps_compass.md) 解释了如何安装带有指南针的GNSS模块。

## 硬件设置

硬件设置取决于飞控、GNSS模块以及它所支持的连接总线——UART/I2C或CAN。

### Pixhawk 标准连接器

当使用支持[Pixhawk 连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)的飞控时，连接 GNSS/罗盘模块最为简便。  
所有遵循此标准的飞控（包括大部分[Pixhawk 标准](../flight_controller/autopilot_pixhawk_standard.md)控制器及许多其他型号）均采用相同的端口连接器和布线方式连接 GNSS 模块。由于这一标准化，许多主流 GNSS/罗盘模块可直接插接到飞控上使用。

若使用通过通用 UART 和串行协议（如 I2C）连接的 GNSS/罗盘模块：

- 主 GNSS/罗盘模块应连接到标有 `GPS1`、`GPS&SAFETY` 或 `GPS` 的 10 针端口（在连接器标准中描述为 "Full GPS + Safety Switch Port"）。  
  该 GPS 应包含 [蜂鸣器](../getting_started/px4_basic_concepts.md#buzzer)、[安全开关](../getting_started/px4_basic_concepts.md#safety-switch) 和 [UI LED](../getting_started/led_meanings.md#ui-led)。  
- （可选）若存在 6 针 `GPS2` 端口（在标准中为 "Basic GPS Port"），可连接次级模块。  
- 这些端口通常专为 u-blox 模块设计（仅限）。

::: info  
端口包含用于 GNSS 的 UART 和用于连接罗盘的 I2C 端口。  
"Full GPS + Safety Switch Port" 包含连接 LED、蜂鸣器和安全开关的额外 I2C 接口。  
您当然可以将 GPS 引脚连接到任意空闲的 UART 作为 GNSS 端口，或将罗盘或蜂鸣器连接到 I2C 端口。  
但若这样做，则需要[配置端口](../peripherals/serial_configuration.md)。  
:::

对于 [DroneCAN](../dronecan/index.md#supported-hardware) GNSS/罗盘模块：

- DroneCan GPS 模块通过标有 `CAN1` 或 `CAN2` 的 4 针 CAN 总线端口连接。

### 其他飞行控制器/GNSS模块

如果您使用的是不符合Pixhawk连接器标准的飞行控制器和GNSS模块组合，则需要特别注意飞行控制器和模块上的连接器引脚排列。您可能需要重新布线/焊接连接器。

:::warning
某些飞行控制器使用软件兼容但连接器不兼容的端口（即使它们使用相同的连接器！），因为它们的引脚顺序不同。
:::

连接器标准的引脚排列在该标准中有所记录。其他控制器和GNSS模块的引脚排列应包含在它们的制造商文档中。

## GNSS配置

通过GPS串口连接的GPS模块的默认配置如下所示。
额外的设备特定配置可能在PX4或制造商设备文档中提供（例如 [Trimble MB-Two > Configuration](../gps_compass/rtk_gps_trimble_mb_two.md#configuration)）。

### 主GPS配置（UART）

Pixhawk上的主GPS配置对U-Blox GPS模块是透明处理的——只需将GPS模块连接到标记为`GPS1`、`GPS&SAFETY`或`GPS`（如果只有一个GPS端口）的端口，所有功能都应正常工作。

默认的[串口配置](../peripherals/serial_configuration.md#default_port_mapping)将`GPS1`配置为GPS端口，通过[GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG)设置协议为`u-blox`（通过[GPS_1_PROTOCOL](../advanced_config/parameter_reference.md#GPS_1_PROTOCOL)），波特率为`0: Auto`（通过[SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD)）。

对于Trimble、Emlid、MTK等类型的GPS，需要相应修改`GPS_1_PROTOCOL`。
对于_Trimble MB-Two_，还需要修改`SER_GPS1_BAUD`，将波特率设置为115200。

<a id="dual_gps"></a>

### 次级GPS配置（UART）

使用次级GPS时，通常将其连接到名为`GPS2`的端口（如果存在），否则连接到任意空闲的UART端口。该端口可能已预先配置，但与主端口不同，这并非始终保证。

为确保端口正确设置，请执行[串口配置](../peripherals/serial_configuration.md)，将[GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)分配到所选端口。

以下步骤展示如何在_QGroundControl_中配置`GPS 2`端口上的次级GPS：

1. [查找并设置](../advanced_config/parameters.md)参数[GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)为**GPS 2**。

   - 打开_QGroundControl_并导航至**机体设置 > 参数**部分。
   - 选择**GPS**标签页，然后打开[GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)参数，并从下拉列表中选择`GPS 2`。

     ![QGC 串口示例](../../assets/peripherals/qgc_serial_config_example.png)

1. 重启机体以使其他参数可见。
1. 选择**串口**标签页，打开[SER_GPS2_BAUD](../advanced_config/parameter_reference.md#SER_GPS2_BAUD)参数（`GPS 2`端口波特率）：将其设置为_Auto_（或Trimble的115200）。

   ![QGC 串口波特率示例](../../assets/peripherals/qgc_serial_baudrate_example.png)

完成次级GPS端口配置后：

1. 配置ECL/EKF2估计器以融合两个GPS系统的数据。
   详细操作请参见：[使用ECL EKF > 双接收器](../advanced_config/tuning_the_ecl_ekf.md#dual-receivers)。

### DroneCAN GNSS配置

DroneCAN GNSS配置详见链接文档（以及特定模块的文档说明）。  
[DroneCAN](../dronecan/index.md#supported-hardware)

### 配置GPS作为偏航/航向源

当使用支持设备_yaw输出_的模块时，GPS可以作为偏航融合的来源。  
这在 [RTK GPS > 配置GPS作为偏航/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source) 中有详细说明。

## 指南针配置

内置指南针部件的校准请参见：[指南针配置](../config/compass.md)。

## GNSS数据概述

PX4使用由大多数GNSS模块可提供的信息子集。  
这些信息被写入[SensorGps](../msg_docs/SensorGps.md) uORB消息，并被估算器用作全局位置估算的输入。  
同时，这些数据通过MAVLink消息进行传输，例如[GPS_RAW_INT](https://mavlink.io/en/messages/common.html#GPS_RAW_INT)和[GPS2_RAW](https://mavlink.io/en/messages/common.html#GPS2_RAW)。

以下是一些对解读GNSS数据有帮助的术语：

- `DOP`：位置稀释度（无量纲单位）。  
  这是衡量卫星位置几何质量及其对GPS接收机计算精度影响的指标。
- `EPH`：水平位置误差标准差（米）。  
  这表示GPS定位的纬度和经度的不确定性。
- `EPV`：垂直位置误差标准差（米）。  
  这表示GPS定位高度的不确定性。

### DOP与EPH/EPV

DOP是基于卫星位置衡量高精度潜力的指标。  
EPH/EPV更为全面：它们是GPS定位误差的直接估算值，同时考虑了卫星几何结构和其他误差来源（如信号噪声和大气影响）。  
当存在显著的信号噪声或大气干扰时，即使DOP较低（卫星几何位置良好），EPH/EPV仍可能较高。

因此，EPH/EPV数值能更直观且实际地反映在当前条件下可预期的GPS精度。

## 开发者信息

- GPS/RTK-GPS
  - [RTK-GPS](../advanced/rtk_gps.md)
  - [GPS驱动](../modules/modules_driver.md#gps)
  - [DroneCAN 示例](../dronecan/index.md)
- 指南针
  - [驱动源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/magnetometer) (指南针)