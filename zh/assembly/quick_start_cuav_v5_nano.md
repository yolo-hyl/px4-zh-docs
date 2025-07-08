# CUAV V5 nano 接线快速入门

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

本快速入门指南介绍了如何为[CUAV V5 nano](../flight_controller/cuav_v5_nano.md)飞控供电并连接其最重要的外设。

![Nano Hero Image](../../assets/flight_controller/cuav_v5_nano/v5_nano_01.png)

## 接线图表概述

下图展示了主要传感器和外设的连接方式（不包括电机和舵机输出）。  
以下章节将详细讲解这些接口。

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_02.png)

| 主接口         | 功能                                                                                                                                                                                                 |
| :------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 电源           | 连接电源模块；提供电源和模拟电压、电流测量                                                                                                                                                           |
| PM2            | [不要与PX4一起使用](../flight_controller/cuav_v5_nano.md#compatibility_pm2)                                                                                                                          |
| TF CARD        | 用于日志存储的SD卡（含卡）                                                                                                                                                                           |
| M1~M8          | PWM输出。可用于控制电机或舵机                                                                                                                                                                        |
| A1~A3          | 捕获引脚（目前PX4不支持）                                                                                                                                                                            |
| nARMED         | 表示FMU的解锁状态。低电平有效（解锁时为低电平）                                                                                                                                                      |
| DSU7           | 用于FMU调试，读取调试信息                                                                                                                                                                            |
| I2C2/I2C3/I2C4 | 连接I2C设备（如外部指南针）                                                                                                                                                                          |
| CAN1/CAN2      | 连接UAVCAN设备（如CAN总线GPS）                                                                                                                                                                       |
| TYPE-C(USB)    | 连接计算机，用于飞控与计算机的通信（如加载固件）                                                                                                                                                     |
| GPS&SAFETY     | 连接Neo GPS，包含GPS、安全开关、蜂鸣器接口                                                                                                                                                           |
| TELEM1/TELEM2  | 连接遥测系统                                                                                                                                                                                         |
| DSM/SBUS/RSSI  | 包含DSM、SBUS、RSSI信号输入接口，DSM接口可连接DSM卫星接收机，SBUS接口连接SBUS遥控接收机，RSSI用于信号强度返回模块 |

::: info
有关更多接口信息，请阅读 [V5 nano手册](http://manual.cuav.net/V5-nano.pdf)。
:::

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_03.png)

::: info
如果控制器无法以推荐/默认方向安装（例如因为空间限制），需要配置飞控软件为实际使用的方向：[飞控方向](../gps_compass/rtk_gps.md)。
:::

## GPS + 指南针 + 安全开关 + LED

推荐使用的GPS模块是_Neo v2 GPS_，其集成了GPS、指南针、安全开关、蜂鸣器和LED状态灯。

::: info
其他GPS模块可能无法正常工作（见[此兼容性问题](../flight_controller/cuav_v5_nano.md#compatibility_gps)）。
:::

GPS/指南针模块应[安装在机体框架上](../assembly/mount_gps_compass.md)，尽可能远离其他电子设备，方向标记应指向机体前方（Neo GPS箭头方向与飞控箭头方向一致）。通过线缆连接到飞控的GPS接口。

::: info
如果使用CAN GPS，请通过线缆连接到飞控的CAN接口。
:::

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_04.png)

## 安全开关

V5+附带的专用安全开关仅在未使用推荐的_Neo v2 GPS_（具有内置安全开关）时才需要使用。

如果没有使用GPS，必须将开关直接连接到`GPS1`端口才能启动机体并飞行（如果使用旧版6针GPS，请阅读底部接口定义以修改线路）。

## 蜂鸣器

如果您未使用推荐的 _Neo v2 GPS_ ，蜂鸣器可能无法工作。

## 无线电控制

如果您需要手动控制机体（PX4的自主飞行模式不需要无线电系统），则必须使用远程控制（RC）无线电系统。您需要选择兼容的发射机/接收机并对其进行绑定以实现通信（请阅读您特定发射机/接收机附带的说明）。

下图展示了如何接入您的遥控接收机（请在套件中找到S.Bus线缆）

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_05.png)

## Spektrum卫星接收器

V5 nano具有专用的DSM线缆。
若使用Spektrum卫星接收器，应将其连接到飞控的`DSM/SBUS/RSSI`接口。

## 电源

_v5 nano_ 套件包含 _HV_PM_ 模块，支持 2~14S 锂聚合物电池。  
将 _HW_PM_ 模块的 6针连接器连接至飞控 `Power` 接口。

:::warning
供电模块无保险丝。  
必须在断电状态下连接外设。
:::

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_06.png)

::: info
供电模块不会为连接至 PWM 输出的外设供电。  
如果连接舵机/执行器，需要通过 BEC 单独供电。
:::

## 遥测系统（可选）

遥测系统允许您从地面站与飞行中的机体进行通信、监控和控制（例如，您可以引导无人机到特定位置，或上传新的任务）。

通信通道通过遥测无线电实现。机体上的无线电应连接到 **TELEM1** 或 **TELEM2** 端口（如果连接到这些端口，则无需进一步配置）。  
另一个无线电连接到您的地面站计算机或移动设备（通常通过USB）。

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_07.png)

<a id="sd_card"></a>## SD卡（可选）

[SD卡](../getting_started/px4_basic_concepts.md#sd-cards-removable-memory)出厂时已预装（您无需进行任何操作）。

## 电机

电机/舵机需按照您机体在[Airframes Reference](../airframes/airframe_reference.md)中指定的顺序连接到主端口。

![quickstart](../../assets/flight_controller/cuav_v5_nano/connection/v5_nano_quickstart_06.png)

## 引脚分配

![V5 nano引脚分配](../../assets/flight_controller/cuav_v5_nano/v5_nano_pinouts.png)

## 更多信息

- [使用 CUAV v5 nano 的 DJI FlameWheel450 机架构建日志](../frames_multicopter/dji_f450_cuav_5nano.md)
- [CUAV V5 nano](../flight_controller/cuav_v5_nano.md)
- [V5 nano 手册](http://manual.cuav.net/V5-nano.pdf) (CUAV)
- [FMUv5 参考设计引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165) (CUAV)
- [CUAV Github](https://github.com/cuav) (CUAV)