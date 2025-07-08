# Holybro X500 V2（Pixhawk 5X 构建）

::: info
Holybro 最初为该套件提供了 [Pixhawk 5X](../flight_controller/pixhawk5x.md)，但撰写本文时已升级为 [Holybro Pixhawk 6C](../flight_controller/pixhawk6c.md)。
本构建日志仍然适用，因为套件装配方式基本相同，且随着飞控升级这一方式预计仍会保持。
:::

本主题提供完整的 [Holybro X500 V2 ARF 套件](https://holybro.com/collections/x500-kits) 构建指南及使用 *QGroundControl* 配置 PX4 的说明。

ARF（"Almost Ready to Fly"）套件为希望快速进入无人机开发且不愿花费大量时间进行硬件配置的用户提供了最短且最简单的装配体验。  
套件包含机架、电机、电调、螺旋桨和电源分配板。

除套件内容外，您还需要准备飞控、遥控器、GPS 和遥控接收机。  
ARF 套件可与 PX4 支持的大部分飞控配合使用。

## 关键信息

- **套件:** [Holybro X500 V2 ARF套件](https://holybro.com/collections/x500-kits)
- **飞行控制器:** [Pixhawk 5X](../flight_controller/pixhawk5x.md)
- **组装时间（约）:** 55分钟（25分钟用于机架组装，30分钟用于飞控安装/配置）

![完整X500 V2套件](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/x500-kit.png)

## 材料清单

Holybro [X500 V2套件](https://holybro.com/collections/x500-kits) 包含几乎所有所需组件：

* X500V2框架套件
  * 机身 - 全碳纤维上下板（144 x 144mm，2mm厚）
  * 臂架 - 高强度超轻16mm碳纤维管
  * 起落架 - 16mm和10mm直径碳纤维管
  * 平台板 - 配备GPS和主流计算模块的安装孔
  * 双10mm Ø杆 x 250mm长导轨安装系统
  * 带双电池绑带的电池固定架
  * 安装工具
* Holybro电机 - 2216 KV880 x6（已过时，请查看[备件列表](https://holybro.com/products/spare-parts-x500-v2-kit)获取最新版本）
* Holybro BLHeli S 电调 20A x4（已过时，请查看[备件列表](https://holybro.com/products/spare-parts-x500-v2-kit)获取最新版本）
* 螺旋桨 - 1045 x4（已过时，请查看[备件列表](https://holybro.com/products/spare-parts-x500-v2-kit)获取最新版本）
* 电源分配板 – XT60电池接口和XT30电调/外设接口
* 摄像头支架（可选，三维文件可从[此处](https://cdn.shopify.com/s/files/1/0604/5905/7341/files/Holybro_X500_V2_3D_Print.rar?v=1665561017)下载）

此构建所需的其他部件（**ARF套件不含**）：
* [Pixhawk 5X飞控](../flight_controller/pixhawk5x.md)
* [M8N GPS](https://holybro.com/products/m8n-gps)
* [电源模块 - PM02D](../power_module/holybro_pm02d.md)
* [433/915 MHz数传电台](../telemetry/holybro_sik_radio.md)

此外，若您需要手动控制无人机，还需要电池（Holybro推荐使用4S 5000mAh）和接收机（[兼容遥控系统](../getting_started/rc_transmitter_receiver.md)）。

## 套件硬件

本节列出机架和自动驾驶仪安装所需的所有硬件。

| 项目 | 描述 | 数量 |
|---|---|---|
| 底板 | 碳纤维（2mm厚） | 1 |
| 顶板 | 碳纤维（1.5mm厚） | 1 |
| 臂 | 碳纤维管（预装电机） | 4 |
| 起落架 - 竖杆 | 碳纤维管+工程塑料 | 2 |
| 起落架 - 横梁 | 碳纤维管+工程塑料+泡沫 | 2 |
| 安装导轨 | 直径：10mm 长度：250mm | 2 |
| 电池安装板 | 厚度：2mm | 1 |
| 电池垫 | 3mm黑色硅胶垫 | 1 |
| 平台板 | 厚度：2mm | 1 |
| 吊环及橡胶环垫片 | 内孔直径：10mm 黑色 | 8 |

![X500V2 ARF套件完整包装内容](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/x500_v2_whats_inside.png)

   _图1_：X500 V2 ARF套件内容

### 电子设备

| 项目描述 | 数量 |
|---|---|
| Pixhawk5x及配套线缆 | 1 |
| M8N GPS模块 | 1 |
| 电源模块PM02D（预焊电调电源线） | 1 |
| 电机2216 KV880（V2升级版） | 4 |
| Holybro BLHeli S ESC 20A x4 | 1 |
| Holybro BLHeli S ESC 20A x4 | 1 |
| 433 MHz / 915 MHz [Holybro遥测无线电](../telemetry/holybro_sik_radio.md) | 1 |

### 所需工具

套件包含组装所需工具，但可能需要额外准备：

- 线缆剪刀
- 精密镊子## 组装

组装预计耗时55分钟（25分钟用于机架，30分钟用于自动驾驶仪安装/配置）

1. 开始组装负载和电池支架。
   将橡胶件压入夹具（不要使用尖锐物品进行推入！）。
   接着，将支架穿过支架横杆，底部基座按图3所示安装。

   ![起落架图1：组件](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/payload_holder_required_stuff.png)

   _图2_：负载支架组件

   ![起落架图2：组装完成](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/payload_holder_assembled.png)

   _图3_：负载支架组装完成

1. 接下来将底板安装到负载支架上。
   你需要图4所示的零件。
   然后使用尼龙螺母安装电源分配板基座（见图5）。
   最后使用8个六角螺丝将底板与负载支架连接（见图7）。

   ![安装底板所需材料](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/topplate_holder_stuff.png)

   _图4_：所需材料

   ![PDB安装基座](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/powerboard-mountbase.png)

   _图5_：PDB安装基座

   ![PDB安装](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/pdb_bottom_plate.png)

   _图6_：使用尼龙螺母安装的PDB

   ![底板最终状态](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/bottom_plate_holder_final.png)

   _图7_：安装在负载支架上的底板

1. 收集图8所示的起落架安装所需材料。
   使用六角螺丝将起落架与底板连接。
   你需要松开每条支架腿上的三个六角螺丝，以便将它们推入碳纤维管中。
   安装完成后务必重新拧紧。

   ![安装起落架所需材料](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/landing_gear_materials.png)

   _图8_：起落架安装所需零件

   ![起落架与机体连接](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/attached_landing_gear.png)

   _图9_：起落架与机体连接

1. 现在将所有臂组件收集用于安装顶板。
   请注意臂组件上的电机编号需与顶板标注的编号匹配。
   幸运的是，电机已预先安装且电调已连接。
   开始时将所有螺丝穿过固定位置（臂组件上有导向装置，如图11所示），并稍微拧紧所有尼龙螺母。
   然后可以将XT30电源连接器连接到电源板上。
   注意信号线需穿过顶板，以便后续连接到Pixhawk。

   <img src="../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/needed_stuff_top_plate.png" width="700" title="臂组件和顶板材料">

   _图10_：连接臂组件所需材料。

   <img src="../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/guide_for_arm_mount.png" width="700" title="臂组件安装指南">

   _图11_：臂组件安装指南

1. 使用六角扳手和螺母驱动器拧紧所有16个螺丝和螺母。

   ![顶板安装完成](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/finalized_top_plate.png)

   _图12_：安装完成的顶板

1. 接下来使用贴纸将Pixhawk安装在顶板上。
   建议将Pixhawk箭头方向与顶板标注的方向保持一致。

   ![飞控安装贴纸](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/pixhawk5x_stickertapes.png)

   _图13_：Pixhawk贴纸

1. 如果需要将GPS安装在伴飞计算机板上，现在可以使用4个螺丝和螺母将其固定。

   <img src="../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/gps_mount_plate.png" width="400" title="将GPS安装座固定到伴飞板">

   _图14_：将GPS安装座固定到伴飞板

1. 使用胶带将GPS粘在GPS桅杆顶部并安装桅杆。
   确保GPS上的箭头指向正前方（见图15）。

   <img src="../../assets/airframes/multicopter/x500_holybro_pixhawk4/gps2.jpg" width="400" title="图16: GPS和桅杆">

   _图15_：GPS和桅杆

1. 最后，连接Pixhawk接口如遥测电台到' TELEM1'和电机信号线。

请参考[Pixhawk 5X 快速入门](../assembly/quick_start_pixhawk5x.md)获取更多信息。

完成。
完全组装后的套件如下所示（套件不包含深度相机）：

![组装完成的套件](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/finalized_x500v2_kit.png)

## PX4 配置

:::tip
完整的PX4安装和配置指南请参考 [基本配置](../config/index.md)。
:::

*QGroundControl* 用于安装PX4自动驾驶仪并针对X500机架进行配置/调校。[下载并安装](http://qgroundcontrol.com/downloads/) 适用于您平台的 *QGroundControl*。

首先更新固件、机架和执行器映射：

- [固件](../config/firmware.md)
- [机架](../config/airframe.md)

  您需要选择 *Holybro X500 V2* 机架 (**多旋翼x > Holybro 500 V2**)

  ![QGroundControl - 选择HolyBro 500机架](../../assets/airframes/multicopter/x500_v2_holybro_pixhawk5x/x500v2_airframe_qgc.png)

- [执行器](../config/actuators.md)
  - 无需更新机体几何参数（因为这是预配置的机架）。
  - 根据接线为输出分配执行器功能。
  - 使用滑动条测试配置。

然后进行必要的设置/校准：

- [传感器方向](../config/flight_controller_orientation.md)
- [指南针](../config/compass.md)
- [加速度计](../config/accelerometer.md)
- [水平校准](../config/level_horizon_calibration.md)
- [遥控器设置](../config/radio.md)
- [飞行模式](../config/flight_mode.md)

建议同时进行：

- [电调校准](../advanced_config/esc_calibration.md)
- [电池估算调校](../config/battery.md)
- [安全设置](../config/safety.md)

## 调整

机架选择会为该机体设置*默认*的自动驾驶参数。  
这些参数足够支持飞行，但针对特定机架结构进行参数调整是个好主意。  

如需了解操作步骤，请从[Autotune](../config/autotune_mc.md)开始。

## 致谢

此构建日志由PX4 Team提供。