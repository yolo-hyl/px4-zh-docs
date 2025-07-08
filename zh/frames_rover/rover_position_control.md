# (已弃用) 车辆位置控制

<LinkedBadge type="warning" text="Experimental" url="../airframes/#experimental-vehicles"/>

::: warning
本信息适用于原始通用车辆模块（继承自固定翼控制器）。
该模块已被新的[阿克曼](../frames_rover/index.md#ackermann)和[差速转向](../frames_rover/index.md#differential)车辆模块替代。
该模块已不再维护且不会获得任何更新。
:::

PX4 支持使用[阿克曼和差速](#rover-types)转向的无人地面车辆(UGVs)。

本节包含若干无人地面车辆框架的构建日志/装配说明以及配置指南。

![Traxxas 车辆图片](../../assets/airframes/rover/traxxas_stampede_vxl/final_side.jpg)

## 陆地车类型

PX4 支持以下陆地车类型：

- **差速转向**：通过左右两侧车轮以不同速度行驶来控制方向。

  这种转向方式常用于推土机、坦克和其他履带式车辆。

- **阿克曼转向**：通过将车轮指向行驶方向来控制方向（[阿克曼几何](https://en.wikipedia.org/wiki/Ackermann_steering_geometry) 补偿了转弯时内外侧车轮速度差异的问题）。  
  这种转向方式用于大多数商用车辆，包括汽车、卡车等。

支持的框架可参见 [机型参考 > 陆地车](../airframes/airframe_reference.md#rover)：这些是诸如 _通用陆地机体（已弃用）_ 等名称中包含 "(已弃用)" 的框架。

## 如何配置Rover

### 阿克曼转向配置

设置具有阿克曼转向的Rover非常简单：

1. 在[Airframe](../config/airframe.md)配置中，选择_Generic Ground Vehicle (Deprecated)_。

   ![选择阿克曼转向的机体配置](../../assets/config/airframe/airframe_rover_ackermann.png)

   点击**Apply and Restart**按钮。

1. 打开[执行器配置与测试](../config/actuators.md)将转向和油门功能映射到飞控输出。

### 差速转向配置

1. 在[Airframe](../config/airframe.md)配置中，选择_Aion Robotics R1 UGV_或_NXP Cup car: DF Robot GPX (Deprecated)_

   ![选择差速转向的机体配置](../../assets/config/airframe/airframe_rover_aion.png)

选择**Apply and Restart**按钮。

1. 打开[执行器配置与测试](../config/actuators.md)并将左/右电机功能映射到飞控输出。

## 仿真

[Gazebo Classic](../sim_gazebo_classic/index.md) 为两种转向类型提供了仿真：

- Ackermann: [ackermann rover](../sim_gazebo_classic/vehicles.md#ackermann-ugv)
- Differential: [r1 rover](../sim_gazebo_classic/vehicles.md#differential-ugv)

## 驱动模式（Rover）

飞行模式（更准确地说是地面机体的驱动模式）为机体提供自动驾驶支持，便于手动驾驶、执行自主任务或将控制权交给外部系统。

PX4地面机体使用已弃用的rover位置控制模块时，仅支持[手动模式](#manual-mode)、[任务模式](#mission-mode)和[外部模式](#offboard-mode)（地面站可能提供其他模式，但这些模式都表现为手动模式）。

### 手动模式

_手动模式_ 是PX4地面机体的唯一手动模式，需要手动控制器（遥控器、游戏手柄、摇杆等）。

在此模式下，当遥控摇杆处于中心位置时电机停止。要移动机体需将摇杆移出中心区域。

一旦松开控制摇杆，它们将返回中心死区。这会关闭电机并使车轮居中。系统没有主动制动，因此机体可能持续移动直至惯性耗尽。

### 任务模式

_任务模式_ 是一种自动模式，使机体执行预定义的自主[任务](../flying/missions.md)计划，该计划已上传至飞控器。任务通常通过地面控制站（GCS）应用程序创建和上传，如[QGroundControl](https://docs.qgroundcontrol.com/master/en/)。

### 外部模式

[外部模式](../flight_modes/offboard.md) 会使得机体遵循通过MAVLink提供的位姿、速度或姿态设定点。并非所有设定点类型对地面机体都有意义或被支持。

::: info
此模式专为通过伴飞计算机和地面站控制机体而设计！
:::

## 更多信息

- [基础配置 > 飞行模式](../config/flight_mode.md) - 如何将遥控器控制开关映射到特定飞行模式
- [飞行模式(多旋翼)](../flight_modes_mc/index.md)
- [飞行模式(固定翼)](../flight_modes_fw/index.md)
- [飞行模式(VTOL)](../flight_modes_vtol/index.md)

## Traxxas Stampede VXL

该机体被选中以研究Pixhawk如何应用于轮式平台。  
我们选择Traxxas车型因其在遥控领域知名度高且具有强大品牌影响力。  
目标是开发一个平台，实现通过自动驾驶仪对轮式无人地面车（UGV）的便捷控制。

![Traxxas Stampede VXL](../../assets/airframes/rover/traxxas_stampede_vxl/stampede.jpg)

### 零件清单

- [Traxxas Stampede](https://traxxas.com/products/models/electric/stampede-vxl-tsm) 除顶部塑料盖外全部使用  
- [Pixhawk Mini (已停产)](../flight_controller/pixhawk_mini.md)  
  - 3DR 10S电源模块  
  - 3DR 433MHz遥测模块（欧盟版）  
- [Spektrum Dxe控制器](http://www.spektrumrc.com/Products/Default.aspx?ProdId=SPM1000) 或其他PX4兼容遥控器  
- [Spektrum Quad Race串口接收机带分集](http://www.spektrumrc.com/Products/Default.aspx?ProdID=SPM4648)  
- [PX4Flow](../sensor/px4flow.md)（已弃用）

### 组装

组装采用木质框架固定所有自动驾驶仪部件。  
测试表明应使用更好的减震绝缘材料，尤其是Pixhawk和Flow模块。

![Stampede底盘](../../assets/airframes/rover/traxxas_stampede_vxl/stampede_chassis.jpg)

![木质面板顶部](../../assets/airframes/rover/traxxas_stampede_vxl/panel_top.jpg)

![木质面板底部](../../assets/airframes/rover/traxxas_stampede_vxl/panel_bottom.jpg)

![Traxxas Stampede最终组装](../../assets/airframes/rover/traxxas_stampede_vxl/final_assembly.jpg)

![侧视图最终组装](../../assets/airframes/rover/traxxas_stampede_vxl/final_side.jpg)

![木质面板安装细节](../../assets/airframes/rover/traxxas_stampede_vxl/mounting_detail.jpg)

本安装方案使用车体自带卡扣固定上层板，为此打印了两块3D支撑件。  
CAD文件请见[此处](https://github.com/PX4/PX4-user_guide/raw/main/assets/airframes/rover/traxxas_stampede_vxl/plane_holders.zip)。

::: warning
**强烈建议**将电调设置为训练模式（参见Traxxas Stampede手册），该模式可将功率降低至50%。
:::

### 输出连接

| PWM输出 | 执行器             |
| -------- | -------------------- |
| MAIN2    | 转向舵机           |
| MAIN4    | 油门（电调输入）   |

### 配置

无人车通过 _QGroundControl_ 配置，方式与其他机体相同。  

关键无人车配置是设置正确的机型：  
1. 在 _QGroundControl_ 中切换到[基础配置](../config/index.md)部分  
1. 选择[机型](../config/airframe.md)标签页  
1. 向下滚动列表找到 **Rover** 图标  
1. 从下拉列表中选择 **Traxxas stampede vxl 2wd**

![选择机型](../../assets/airframes/rover/traxxas_stampede_vxl/airframe_px4_rover_traxxas_stampede_vxl_2wd.jpg)

### 使用

目前PX4仅支持在连接遥控器时使用任务模式和手动模式。  
使用任务模式时，首先通过QGC上传新任务。然后，在**上锁前**选择 `MISSION` 模式并上锁。

:::warning
必须确保任务**仅包含**普通航点（即不含起飞航点等），且**所有**航点高度必须设为0才能正确执行。  
否则无人车会持续绕航点旋转。
:::

正确任务配置如下图所示：

![任务](../../assets/airframes/rover/traxxas_stampede_vxl/correct_mission.jpg)

## 视频

<iframe width="740" height="416" src="https://www.youtube.com/embed/N3HvSKS3nCw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>