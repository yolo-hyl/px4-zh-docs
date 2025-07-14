

# Holybro QAV250 + Pixhawk 4 Mini 机架组装指南（已停产）

::: info
_Holybro Pixhawk 4 Mini QAV250套件_ 已经停产。

这些说明仍然保留在此，因为基于Pix32 v6的非常相似的套件[可在此处获取](https://holybro.com/products/qav250-kit)。
因此这些说明仍然可以遵循（并可能更新到Pix32 v6）。
:::

完整套件包括碳纤维QAV250竞速机架、飞控以及几乎所有其他所需组件（除电池和接收器外）。
套件有带和不带FPV支持的版本。
本指南提供使用*QGroundControl*完成套件组装和PX4配置的完整说明。

关键信息

- **机架:** Holybro QAV250
- **飞控:** [Pixhawk 4 Mini](../flight_controller/pixhawk4_mini.md)
- **组装时间（约）:** 3.5小时（2小时机架组装，1.5小时自动驾驶仪安装/配置）

![组装好的Holybro QAV250搭配Pixhawk4 Mini](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/qav250_hero.jpg)

## 快速入门指南

[Pixhawk 4 Mini QAV250 Kit 快速入门指南](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_controller/pixhawk4mini/pixhawk4mini_qav250kit_quickstart_web.pdf)

## 材料清单

Holybro [QAV250 Kit](https://holybro.com/products/qav250-kit) 套件包含了几乎所有必需的组件:
* [Holybro Transceiver Telemetry Radio V3](../telemetry/holybro_sik_radio.md)
* Holybro电源模块
* 完全组装的电源管理板（含电调）
* 电机 - DR2205 KV2300
* 5”塑料桨叶
* 碳纤维250机体结构及配件
* Foxer相机
* Vtx 5.8GHz

此外还需要电池和接收机（+兼容遥控器）。
此组装使用：
- 接收机: [FrSSKY D4R-II](https://www.frsky-rc.com/product/d4r-ii/)
- 电池: [4S 1300 mAh](http://www.getfpv.com/lumenier-1300mah-4s-60c-lipo-battery-xt60.html)

## 硬件

本节列出了所有用于机架和自动驾驶仪安装的硬件。

### 框架 QAV250

描述 | 数量
--- | ---
一体式框架板 | 1
飞控盖板 |  1
PDB |  1
相机板 |  1
35mm 绝缘柱 |  6
Vinyl 螺丝和螺母 |  4
15mm 钢螺丝 |  8
钢螺母 |  8
7mm 钢螺丝 |  12
魔术贴电池绑带 |  1
电池填充泡沫 |  1
起落架垫片 |  4

![QAV250 框架组件](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/frame_components.jpg)

### 电子设备

| 描述 | 数量 |
| --- | --- |
| 电机 - DR2205 KV2300 | 4 |
| 带电调的完整组装电源管理板 | 4 |
| Holybro电源模块 | 1 |
| Fr-sky D4R-II接收机 | 1 |
| Pixhawk 4 mini | 1 |
| Holybro GPS Neo-M8N | 1 |
|[Holybro收发一体遥测电台V3](../telemetry/holybro_sik_radio.md)| 1 |
| 电池 Lumenier 1300 mAh 4S 14.8V | 1 |
| Vtx 5.8gHz | 1 |
| FPV相机（完整套件 - 仅） | 1 |

下图显示了机架和电子组件。

![QAV250机架/Pixhawk 4 Mini电子设备装配前](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/frame_and_electronics_components.jpg)

## 装配

组装框架预计需要2小时，1.5小时用于在*QGroundControl*中安装自动驾驶仪和配置机体。

### 所需工具

本组装过程中使用的工具如下：

- 2.0mm 六角螺丝刀  
- 3mm 十字螺丝刀  
- 电线剪  
- 精密镊子  

![QAV250组装所需工具](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/assembly_tools.jpg)

### 机架组装 

1. 用15mm螺丝将臂连接到按钮板上（如图所示）:

   ![QAV250 将臂连接到按钮板](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/1_button_plate_add_arms.jpg)
1. 将短板覆盖在臂上

   ![QAV250 将短板覆盖在臂上](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/2_short_plate_over_arms.jpg)
1. 在15mm螺丝上加上螺母（如下一步所示）
1. 将塑料螺丝插入指示的孔中（注意该机架部分在机体完成后会朝下）。
   ![QAV250 在15mm螺丝上加螺母并把塑料螺母放入孔中](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/3_nuts_screws_holes.jpg)
1. 将塑料螺母拧到螺丝上（如图翻转）
   ![QAV250 塑料螺母拧到螺丝上](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/4_plastic_nuts_on_screws.jpg)
1. 将电源模块套在塑料螺丝上，然后加上塑料隔离柱
   ![QAV250 加装电源模块和隔离柱](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/5_power_module_on_screws.jpg)
1. 将飞控板安装在隔离柱上（位于电源模块上方）
   ![QAV250 加装飞控板](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/6_flight_controller_plate.jpg)
1. 安装电机。电机上有箭头指示旋转方向。
   ![QAV250 加装电机](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/7_motors.jpg)
1. 使用套件中的双面胶将 *Pixhawk 4 Mini* 粘贴到飞控板上。
   ![QAV250 加装双面胶](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/8_double_sided_tape_controller.jpg)
1. 将电源模块的"power"线连接到 *Pixhawk 4 mini*。
   ![QAV250 给Pixhawk供电](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/9_power_module_power_pixhawk.jpg)
1. 将铝制隔离柱安装到按钮板上
   ![QAV250 铝制隔离柱](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/10_aluminium_standoffs.jpg)
1. 将电调与电机连接并固定。本图显示了电机的顺序和旋转方向。
   ![QAV250 连接电调](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/11_escs.jpg)

   将电机连接到电调上，确保电机转向正确，如果电机转向相反，将电调上的A线接到C垫，C线接到A垫。
   
   :::warning
   测试电机方向时请取下螺旋桨。
   :::
   
   ![QAV250 连接电调电源](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/11b_escs.jpg)
1. 将电调的信号线按照正确顺序连接到Pixhawk的PWM输出口（见上图）

   ![QAV250 连接电调到Pixhawk PWM](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/12_escs_pixhawk.jpg)
1. 连接接收机。 
   * 如果使用PPM接收机请连接到PPM接口。
   
     ![QAV250 连接PPM接收机](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/13_rc_receiver_ppm.jpg)
   * 如果使用SBUS接收机请连接到RC IN接口
   
     ![QAV250 连接SBUS接收机](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/13_rc_receiver_sbus.jpg)
1. 连接数传模块。用双面胶粘贴模块并连接到数传接口。

   ![QAV250 数传模块](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/14_telemetry.jpg)
1. 连接GPS模块

   ![QAV250 连接GPS](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/15a_connect_gps.jpg)

   将模块粘贴在顶板上（使用提供的3M胶或双面胶）。然后按图示将顶板安装在隔离柱上
   
   ![QAV250 连接GPS](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/15b_attach_gps.jpg)
1. 最后一个"必装"步骤是安装扎带固定电池

   ![QAV250 电池扎带](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/16_velcro_strap.jpg)


基础机架组装已完成（如果您需要更多信息，可以参考[Pixhawk 4 Wiring Quickstart](../assembly/quick_start_pixhawk4.md)中的组件连接说明）。 

如果您使用的是"基础版"套件，现在可以跳转到[Install/Configure PX4](#px4-configuration)部分查看安装/配置说明。

### FPV组装

该套件的"完整"版本附带FPV系统，安装在机体前方如图所示。

![QAV250 FPV安装](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_camera.jpg)

安装套件的步骤如下：

1. 在框架上安装相机支架  
   ![相机连接](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_camera_bracket.jpg)
1. 将相机安装在支架上  
   ![支架上的相机](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_camera_on_bracket.jpg)
1. 完整套件的电源模块配有预接线，可连接视频发射器和相机：  
   ![连接FPV](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_connection_board.jpg)  
   - 连接相机接口  
     ![相机连接](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_camera_connection.jpg)  
     线缆定义：蓝色=电压传感器，黄色=视频输出，黑色=地线，红色=+电压  
   - 连接视频发射器(VTX)接口  
     ![视频发射器连接](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_video_transmitter_connection.jpg)  
     线缆定义：黄色=视频输出，黑色=地线，红色=+电压  
1. 使用胶带将视频发射器和OSD板固定在框架上  

::: info
如果需要手动布线，下图展示了相机、VTX和电源模块之间的所有连接：  
![QAV250 FPV布线](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/fpv_connection.jpg)  
:::

## PX4 配置

使用 *QGroundControl* 安装 PX4 自主飞行控制器，并针对 QAV250 机体进行配置/调参。  
[下载并安装](http://qgroundcontrol.com/downloads/) *QGroundControl* 到您的平台。

:::tip
完整的 PX4 安装和配置指南请参阅 [基础配置](../config/index.md)。
:::

首先更新固件、机体和执行器映射：

- [固件](../config/firmware.md)
- [机体](../config/airframe.md)
  
  ::: info
  您需要选择 *HolyBro QAV250* 机体 (**四旋翼x > HolyBro QAV250**)。

  ![QGC - 选择 HolyBro QAV250 机体](../../assets/airframes/multicopter/qav250_holybro_pixhawk4_mini/qgc_airframe_holybro_qav250.png)
  :::
  
- [执行器](../config/actuators.md)
  - 您无需更新机体几何结构（因为这是预配置的机体）。
  - 根据接线将执行器功能分配到输出端口。
    - 对于 Pixhawk 4 Mini 和其他没有 [I/O 板](../hardware/reference_design.md#main-io-function-breakdown) 的控制器，需要在配置界面的 `PWM AUX` 选项卡中分配执行器到输出端口。
    - Pix32 v6 有 I/O 板，因此可以分配到 AUX 或 MAIN 端口。    
  - 使用滑块测试配置。

然后执行必要的设置/校准：

- [传感器方向](../config/flight_controller_orientation.md)
- [指南针](../config/compass.md)
- [加速度计](../config/accelerometer.md)
- [水平地平线校准](../config/level_horizon_calibration.md)
- [遥控器设置](../config/radio.md)
- [飞行模式](../config/flight_mode.md)

理想情况下您还应该进行：

- [电调校准](../advanced_config/esc_calibration.md)
- [电池估算调参](../config/battery.md)
- [安全设置](../config/safety.md)

## 调参

机架选择会为该机架设置*默认的*自动驾驶参数。
这些参数可能已经足够用于飞行，但你应该对每个机架构建进行调参。

有关如何操作的说明，请从[自动调参](../config/autotune_mc.md)开始。

## 致谢

本构建日志由PX4 测试团队提供。