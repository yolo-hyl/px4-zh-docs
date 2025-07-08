# DJI FlameWheel 450 + CUAV V5 nano 组装

本主题提供了使用 *QGroundControl* 完成套件组装和 PX4 配置的完整指南。

关键信息

- **机架:** DJI F450
- **飞控:** [CUAV V5 nano](../flight_controller/cuav_v5_nano.md)
- **组装时间（约）:** 90分钟（45分钟用于机架，45分钟用于飞控安装/配置）

![成品效果](../../assets/airframes/multicopter/dji_f450_cuav_5nano/f450_cuav5_nano_complete.jpg)

## 材料清单

本组装所需的组件包括：
- 飞控：[CUAV V5 nano](https://store.cuav.net/shop/v5-nano/)：
  - GPS：[CUAV NEO V2 GPS](https://store.cuav.net/index.php?id_product=97&id_product_attribute=0&rewrite=cuav-new-ublox-neo-m8n-gps-module-with-shell-stand-holder-for-flight-controller-gps-compass-for-pixhack-v5-plus-rc-parts-px4&controller=product&id_lang=1)
  - 电源模块
- 机架：[DJI F450](https://www.amazon.com/Flame-Wheel-Basic-Quadcopter-Drone/dp/B00HNMVQHY)
- 螺旋桨：[DJI Phantom 带内置螺母升级螺旋桨 9.4x5](https://www.masterairscrew.com/products/dji-phantom-built-in-nut-upgrade-propellers-in-black-mr-9-4x5-prop-set-x4-phantom)
- 电池：[Turnigy 高容量 5200mAh 3S 12C 锂聚合物电池组 带XT60](https://hobbyking.com/en_us/turnigy-high-capacity-5200mah-3s-12c-multi-rotor-lipo-pack-w-xt60.html?___store=en_us)
- 数传：[Holybro Transceiver Telemetry Radio V3](../telemetry/holybro_sik_radio.md)
- 遥控接收机：[FrSky D4R-II 2.4G 4CH ACCST 数传接收机](https://www.banggood.com/FrSky-D4R-II-2_4G-4CH-ACCST-Telemetry-Receiver-for-RC-Drone-FPV-Racing-p-929069.html?cur_warehouse=GWTR)
- 电机：[DJI E305 2312E 电机 (960kv,顺时针)](https://www.amazon.com/DJI-E305-2312E-Motor-960kv/dp/B072MBMCZN)
- 电调：Hobbywing XRotor 20A APAC 无刷电调 3-4S 适用于多旋翼

此外，我们还使用了FrSky Taranis遥控器。
您还需要扎带、双面胶、电烙铁。

下图显示了机架和电子组件。

![所有组件](../../assets/airframes/multicopter/dji_f450_cuav_5nano/all_components.jpg)

## 硬件

### 机架

本节列出所有机架硬件。

描述 | 数量
---|---
DJI F450 底板 | 1
DJI F450 顶板 | 1
DJI F450 带起落架的支架 | 4
M3*8 螺丝 | 18
M2 5*6 螺丝 | 24
魔术贴电池绑带 | 1
DJI Phantom 内置螺母升级螺旋桨 9.4x5 | 1

![F450 机架组件](../../assets/airframes/multicopter/dji_f450_cuav_5nano/f450_frame_components.png)

### CUAV v5 nano 套件

本节列出CUAV v5 nano套件中的组件。

描述 | 数量（默认套件） | 数量（+GPS套件）
--- | --- | ---
V5 nano 飞控 | 1 | 1
杜邦线 | 2 | 2
I2C/CAN线 | 2 | 2
ADC 6.6线 | 2 | 2
SBUS信号线 | 1 | 1
IRSSI线 | 1 | 1
DSM信号线 | 1 | 1
ADC 3.3线 | 1 | 1
调试线 | 1 | 1
安全开关线 | 1 | 1
电压电流线 | 1 | 1
PW-Link模块线 | 1 | 1
电源模块 | 1 | 1
SanDisk 16GB 存储卡 | 1 | 1
12C扩展板 | 1 | 1
TTL板 | 1 | 1
NEO GPS | - | 1
GPS支架 | - | 1

### 电子设备

描述 | 数量
--- | --- 
CUAV V5 nano | 1
CUAV NEO V2 GPS | 1
Holibro 通信模块 | 1
FrSky D4R-II 2.4G 4CH ACCST 通信接收机 | 1
DJI E305 2312E 电机（800kv,顺时针） | 4
Hobbywing XRotor 20A APAC 无刷电调 | 4
电源模块（含于CUAV V5 nano套件） | 1
Turnigy 高容量 5200mAh 3S 12C 锂电池组 带XT60 | 1

### 所需工具

组装过程将使用以下工具：

- 2.0mm 六角螺丝刀
- 3mm 十字螺丝刀
- 电线剪
- 精密镊子
- 电烙铁

![所需工具](../../assets/airframes/multicopter/dji_f450_cuav_5nano/required_tools.jpg)

## 组装

预计组装时间约为90分钟（45分钟用于机架，45分钟用于安装飞控和配置机体）。

1. 使用提供的螺丝将4个臂安装到底板上。

   ![臂安装到底板](../../assets/airframes/multicopter/dji_f450_cuav_5nano/1_attach_arms_bottom_plate.jpg)

1. 将电调（Electronic Speed Controller）焊接在电路板上，正极（红色）和负极（黑色）。

   ![焊接电调](../../assets/airframes/multicopter/dji_f450_cuav_5nano/2_solder_esc.jpg)

1. 焊接电源模块，正极（红色）和负极（黑色）。

   ![焊接电源模块](../../assets/airframes/multicopter/dji_f450_cuav_5nano/3_solder_power_module.jpg)

1. 根据电机位置将电机插入电调。

   ![插入电机](../../assets/airframes/multicopter/dji_f450_cuav_5nano/4_plug_in_motors.jpg)

1. 将电机安装到对应的臂上。

   ![电机安装到臂（白色）](../../assets/airframes/multicopter/dji_f450_cuav_5nano/5a_attach_motors_to_arms.jpg)
   ![电机安装到臂（红色）](../../assets/airframes/multicopter/dji_f450_cuav_5nano/5b_attach_motors_to_arms.jpg)

1. 将顶板安装到腿的顶部（使用螺丝固定）。

   ![安装顶板](../../assets/airframes/multicopter/dji_f450_cuav_5nano/6_add_top_board.jpg)

1. 在*CUAV V5 nano*飞控上添加减震泡沫。

   ![减震泡沫](../../assets/airframes/multicopter/dji_f450_cuav_5nano/7a_attach_cuav5nano.jpg)
   ![减震泡沫](../../assets/airframes/multicopter/dji_f450_cuav_5nano/7b_attach_cuav5nano.jpg)

1. 使用双面胶将FrSky接收机粘到底板上。

   ![使用双面胶安装FrSky接收机](../../assets/airframes/multicopter/dji_f450_cuav_5nano/8_attach_frsky.jpg)

1. 使用双面胶将数传模块粘在机体底板上。

   ![安装数传模块](../../assets/airframes/multicopter/dji_f450_cuav_5nano/9a_telemtry_radio.jpg)
   ![安装数传模块](../../assets/airframes/multicopter/dji_f450_cuav_5nano/9b_telemtry_radio.jpg)

1. 将铝制延长柱安装在底板上并固定GPS。

   ![铝制延长柱](../../assets/airframes/multicopter/dji_f450_cuav_5nano/10_aluminium_standoffs.jpg)

1. 将数传（`TELEM1`）、GPS模块（`GPS/SAFETY`）、遥控接收机（`RC`）、所有4个电调（`M1-M4`）和电源模块（`Power1`）连接到飞控上。
   ![外设连接到飞控](../../assets/airframes/multicopter/dji_f450_cuav_5nano/12_fc_attach_periperhals.jpg)
   
   ::: info
   电机顺序在 [Airframe Reference > Quadrotor x](../airframes/airframe_reference.md#quadrotor-x) 中定义
   :::

完成了！
最终组装效果如下：

![完成设置](../../assets/airframes/multicopter/dji_f450_cuav_5nano/f450_cuav5_nano_complete.jpg)

## PX4配置

*QGroundControl* 用于安装PX4自动驾驶仪并根据机架进行配置/调整。
[下载并安装](http://qgroundcontrol.com/downloads/) *QGroundControl* 到您的平台。

:::tip
完整安装和配置PX4的说明请参考 [基本配置](../config/index.md)。

首先更新固件、机架、几何参数和输出：

- [固件](../config/firmware.md)
- [机架](../config/airframe.md)

  ::: info
  您需要选择 *Generic Quadcopter* 机架 (**Quadrotor x > Generic Quadcopter**)。

  ![QGroundControl - 选择通用四旋翼机架](../../assets/airframes/multicopter/dji_f450_cuav_5nano/qgc_airframe_generic_quadx.png)
  :::
  
- [执行器](../config/actuators.md)
  - 更新机体几何参数以匹配机架。
  - 按照接线分配执行器功能到输出。
  - 使用滑块测试配置。

然后进行必填的设置/校准：

- [传感器方向](../config/flight_controller_orientation.md)
- [指南针](../config/compass.md)
- [加速度计](../config/accelerometer.md)
- [水平地平线校准](../config/level_horizon_calibration.md)
- [遥控器设置](../config/radio.md)
- [飞行模式](../config/flight_mode.md)

  ::: info
  在本构建中，我们在接收机上设置了 *Stabilized*、*Altitude* 和 *Position* 三种模式（映射到单个通道 - 5）。
  这是推荐给初学者的最小模式集。
  :::

理想情况下还应进行：

- [电调校准](../advanced_config/esc_calibration.md)
- [电池估算调整](../config/battery.md)
- [安全](../config/safety.md)

## 调参

机体选择会为框架设置 *默认* 自动驾驶仪参数。  
这些参数可能足够用于飞行，但你应该对每个机体构建进行调参。  

如需操作指导，请从 [Autotune](../config/autotune_mc.md) 开始。

## 视频

<lite-youtube videoid="b0bKNdDqVHw" title="CUAV Nano"/>## 致谢

本构建日志由Dronecode Test Flight Team提供。