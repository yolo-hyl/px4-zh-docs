# RTK GNSS（GPS）

[实时动态（RTK）](https://en.wikipedia.org/wiki/Real_Time_Kinematic) GNSS/GPS系统可提供厘米级精度，使PX4能够用于如精密测绘（需要精确点位精度）等应用。

此功能需要在笔记本电脑/PC上运行_QGroundControl_，并通过WiFi或数传电台与机体连接到地面站笔记本电脑。

::: info
部分RTK GNSS配置可提供偏航/航向信息，作为指南针的替代方案：

- [RTK GPS偏航角与双u-blox F9P](../gps_compass/u-blox_f9p_heading.md)。
- GPS直接输出偏航角（见下表）。

:::

## 支持的设备

PX4 支持 [u-blox M8P](https://www.u-blox.com/en/product/neo-m8p)、[u-blox F9P](https://www.u-blox.com/en/product/zed-f9p-module) 和 [Trimble MB-Two](https://www.trimble.com/Precision-GNSS/MB-Two-Board.aspx) GPS，以及集成了这些模块的产品。

以下为与 PX4 兼容的 RTK 设备（不含已停产设备）。表格中列出了支持输出偏航角的设备，以及在使用两个机载单元时可提供偏航角的设备。同时标注了通过 CAN 总线连接的设备，以及支持 PPK（后处理运动学）的设备。

设备 | GPS | 电子罗盘 | [DroneCAN](../dronecan/index.md) | [GPS偏航角](#配置-gps-为-偏航角-来源) | PPK
:--- | :---: | :---:  | :---:  | :---:  | :---:
[ARK RTK GPS](../dronecan/ark_rtk_gps.md) | F9P  | BMM150 | ✓ | [双 F9P][DualF9P] |
[ARK MOSAIC-X5 RTK GPS](../dronecan/ark_mosaic__rtk_gps.md) | Mosaic-X5  | IIS2MDC | ✓ | [Septentrio 双天线][SeptDualAnt] |
[CUAV C-RTK GPS](../gps_compass/rtk_gps_cuav_c-rtk.md) | M8P/M8N | ✓ | | |
[CUAV C-RTK2](../gps_compass/rtk_gps_cuav_c-rtk2.md) | F9P |  ✓ | | [双 F9P][DualF9P] |
[CUAV C-RTK 9Ps GPS](../gps_compass/rtk_gps_cuav_c-rtk-9ps.md) | F9P | RM3100 | | [双 F9P][DualF9P] |
[CUAV C-RTK2 PPK/RTK GNSS](../gps_compass/rtk_gps_cuav_c-rtk.md) | F9P | RM3110 | | ✓ |  
[SparkFun GPS-RTK2 Board - ZED-F9P](https://www.sparkfun.com/products/15136) | F9P |  ✓ | | [双 F9P][DualF9P] |
[Trimble MB-Two](../gps_compass/rtk_gps_trimble_mb_two.md) | F9P | ✓ | | ✓ | |

<!-- 上表中使用的链接 -->

[RaccnLabL1L2ZED-F9P ext_ant]: https://docs.raccoonlab.co/guide/gps_mag_baro/gnss_external_antenna_f9p_v320.html
[RaccoonLab L1/L2 ZED-F9P]: https://docs.raccoonlab.co/guide/gps_mag_baro/gps_l1_l2_zed_f9p.html
[DualF9P]: ../gps_compass/u-blox_f9p_heading.md
[SeptDualAnt]: ../gps_compass/septentrio.md#gnss-based-heading
[UnicoreDualAnt]: ../gps_compass/rtk_gps_holybro_unicore_um982.md#enable-gps-heading-yaw
[DATAGNSS GEM1305 RTK]: ../gps_compass/rtk_gps_gem1305.md

说明：

- "✓" 或具体型号表示支持该功能，"✘" 或空白表示不支持。"?" 表示未知。
- 若可能且相关，会标注具体部件名称（例如 GPS 列中的 "✓" 表示存在 GPS 模块但型号未知）。
- 部分 RTK 模块只能用于特定角色（基准站或流动站），而其他模块可互换使用。
- 列表可能省略部分已停产但仍受支持的硬件。例如 [CubePilot Here+ RTK GPS](../gps_compass/rtk_gps_hex_hereplus.md) 已停产，未来版本可能会从列表中移除。若需了解已停产模块的信息，请查阅早期版本。

## 定位设置/配置

RTK定位需要一对[RTK GNSS设备](#supported-devices)：一个"基准"模块用于地面站，一个"移动"模块用于机体。

此外还需要：

- 一台安装了_QGroundControl_的_笔记本/PC_（Android/iOS版本的QGroundControl不支持RTK）
- 机体需通过WiFi或遥测无线电与笔记本连接。

::: info
_QGroundControl_配合基准模块理论上可以为多架机体/移动模块启用RTK GPS，但截至目前该用例尚未经过测试。
:::

### 硬件设置

#### 移动RTK模块（机体）

连接方式和线缆/接头需求取决于所选RTK模块（以及[飞控](../flight_controller/index.md)）。

大多数模块通过飞控的GPS端口连接，与普通GPS模块相同。部分模块连接到[CAN](../can/index.md)总线（即使用[DroneCAN](../dronecan/index.md)）。

请参阅[所选设备文档](#supported-devices)、通用[GNSS硬件/配置设置](../gps_compass/index.md#hardware-setup)以及[DroneCAN](../dronecan/index.md)以获取布线和配置的更多信息。

#### 基准RTK模块（地面）

通过USB将基准模块连接到_QGroundControl_。使用期间基准模块不能移动。

:::tip
选择一个无需移动、视野开阔且远离建筑物的位置。通常使用三脚架或屋顶安装能有效提升基准GPS高度。
:::

#### 遥测无线电/WiFi

机体和地面控制笔记本必须通过[wifi或无线电遥测链路](../telemetry/index.md)连接。

链路_必须_使用MAVLink 2协议，因为它能更高效利用信道。该协议通常默认启用，否则请按照下方[MAVLink2配置说明](#mavlink2)操作。

### RTK连接流程

RTK GPS连接本质上是即插即用：

1. 启动_QGroundControl_，并通过USB将基准RTK GPS连接到地面站。设备将被自动识别。
1. 启动机体并确保其已连接到_QGroundControl_。

   :::tip
   _QGroundControl_在连接RTK GPS时，顶部状态栏会显示RTK GPS状态图标（除正常GPS状态图标外）。设置过程中图标为红色，RTK GPS激活后变为白色。点击图标可查看当前状态和RTK精度。
   :::

1. _QGroundControl_开始RTK设置流程（称为"Survey-In"）。

   Survey-In是获取基准站精确位置的启动程序。该过程通常持续数分钟（在达到[RTK设置](#rtk-gps-settings)中指定的最短时间和精度后结束）。

   点击RTK GPS状态图标可跟踪进度。

   ![survey-in](../../assets/qgc/setup/rtk/qgc_rtk_survey-in.png)

1. Survey-in完成后：

   - RTK GPS图标变为白色，_QGroundControl_开始向机体传输位置数据：

     ![RTK streaming](../../assets/qgc/setup/rtk/qgc_rtk_streaming.png)

   - 机体GPS切换到RTK模式。新模式显示在_正常_ GPS状态图标中（`3D RTK GPS Lock`）：

     ![RTK GPS Status](../../assets/qgc/setup/rtk/qgc_rtk_gps_status.png)

### 配置GPS作为偏航/航向源

当使用支持双天线的单设备或某些[RTK GPS双u-blox F9P配置](../gps_compass/u-blox_f9p_heading.md)时，GPS可作为偏航融合的来源。使用GPS作为航向源的优势在于偏航计算不受磁力干扰影响。

两种方法均通过比较GNSS信号到达两个分离天线的时间差实现。天线间距取决于设备，通常在50厘米左右（请查阅制造商文档）。

支持该功能的设备列在上表的**GPS Yaw**列中，如[Septentrio AsteRx-m3 Pro](../gps_compass/septentrio_asterx-rib.md)、[Holybro H-RTK Unicore UM982 GPS](../gps_compass/rtk_gps_holybro_unicore_um982.md)和[Trimble MB-Two](../gps_compass/rtk_gps_trimble_mb_two.md)。表格中的链接指向设备特定的PX4配置。

通常使用GNSS作为偏航信息源时需要配置以下参数：

| 参数                        | 设置                                                                                                                                                     |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [GPS_YAW_OFFSET][GPS_YAW_OFFSET] | _基线_（两个GPS天线之间的连线）与机体x轴（前后轴，如[此处][fc_orientation]所示）形成的夹角。 |
| [EKF2_GPS_CTRL][EKF2_GPS_CTRL]   | 设置位位置3 "双天线航向" 为 `1`（即参数值加8）。                                                                         |

<!-- links used in table above -->

[GPS_YAW_OFFSET]: ../advanced_config/parameter_reference.md#GPS_YAW_OFFSET
[EKF2_GPS_CTRL]: ../advanced_config/parameter_reference.md#EKF2_GPS_CTRL
[fc_orientation]: ../config/flight_controller_orientation.md#calculating-orientation

:::tip
如果使用此功能，其他配置应正常设置（如[RTK定位](../gps_compass/rtk_gps.md#positioning-setup-configuration)）。
:::

### 可选PX4配置

以下设置可能需要通过_QGroundControl_进行调整：

#### RTK GPS设置

#### MAVLink2协议

#### 调参

#### 双接收器配置

- 视频演示会很实用。
- 展示基准站位置、RTK移动模块连接、基准校准过程的短精度校准。

## 更多信息

- [RTK-GPS（PX4集成）](../advanced/rtk_gps.md): 关于将RTK-GPS支持集成到PX4中的开发者信息。
- [Real Time Kinematic](https://en.wikipedia.org/wiki/Real_Time_Kinematic) (维基百科)