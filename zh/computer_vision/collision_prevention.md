# 防撞

_防撞_ 功能可在机体撞上障碍物前自动减速并停止机体。  
该功能可在多旋翼机体使用基于加速度的[位置模式](../flight_modes_mc/position.md)（或VTOL机体在MC模式下）启用。

该功能可在多旋翼机体的[位置模式](../flight_modes_mc/position.md)下启用（要求 [MPC_POS_MODE](#MPC_POS_MODE) 设置为 `Acceleration based`），并可使用来自以下数据源的传感器信息：外部计算单元、通过MAVLink连接的外部测距仪、连接至飞控的测距仪，或以上任意组合。

如果传感器探测范围不足，防撞功能可能会限制机体最大速度！  
它还会阻止在没有传感器数据的方向上移动（例如：若您没有后方传感器数据，则无法向后飞行）。

:::tip
如果飞行速度至关重要，请在不需要时考虑禁用防撞功能。
:::

:::tip
请确保在启用防撞功能时，所有期望飞行的方向都有传感器/传感器数据。
:::

## 概述

机体通过限制当前速度来减速以接近障碍物，并调整加速度目标值以避免碰撞轨迹。  
若需远离（或平行于）障碍物，用户必须指令机体移动至不会使其更靠近障碍物的目标点。  
算法会在判定请求目标点两侧固定范围内存在“更优”目标点时，对目标方向进行细微调整。

当防撞系统正在主动控制速度目标值时，用户可通过_QGroundControl_获得通知。

PX4软件的配置将在下一节中介绍。  
若您使用连接至飞控的测距传感器进行防撞，需按照[PX4距离传感器](#rangefinder)的说明进行连接和配置。  
若使用伴飞计算机提供障碍物信息，请参见下方的[伴飞配置](#companion)。

## 支持的距离传感器 {#rangefinder}

### Lanbao PSK-CM8JL65-CC5 [已停产]

撰写时 PX4 允许您即插即用 [Lanbao PSK-CM8JL65-CC5](../sensor/cm8jl65_ir_distance_sensor.md) 红外距离传感器进行防撞检测，仅需少量附加配置：

- 首先[安装并配置传感器](../sensor/cm8jl65_ir_distance_sensor.md)，并通过 [CP_DIST](#CP_DIST) 启用防撞检测（如上所述）。
- 通过 [SENS_CM8JL65_R_0](../advanced_config/parameter_reference.md#SENS_CM8JL65_R_0) 设置传感器方向。

### LightWare LiDAR SF45 旋转激光雷达

PX4 v1.14（及后续版本）支持 [LightWare LiDAR SF45](../sensor/sf45_rotating_lidar.md) 旋转激光雷达，可提供 320 度感知范围。

### 其他距离传感器

其他传感器可通过修改驱动代码启用，包括设置传感器方向和视场角：

- 在特定端口安装并配置距离传感器（参见[sensor-specific docs](../sensor/rangefinders.md)），并使用 [CP_DIST](#CP_DIST) 启用防撞检测。
- 修改驱动以设置方向。
  可通过模仿 `SENS_CM8JL65_R_0` 参数实现（您也可能在传感器 _module.yaml_ 文件中硬编码方向为类似 `sf0x start -d ${SERIAL_DEV} -R 25` 的形式 - 其中 25 等效于 `ROTATION_DOWNWARD_FACING`）。
- 修改驱动以在距离传感器 UORB 主题中设置 _field of view_（`distance_sensor_s.h_fov`）。

## PX4（软件）设置

在 _QGroundControl_ 中通过[设置以下参数](../advanced_config/parameters.md)配置防撞功能：

| 参数                                                                                          | 描述                                                                                                                                                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="CP_DIST"></a>[CP_DIST](../advanced_config/parameter_reference.md#CP_DIST)                   | 设置允许的最小距离（机体可接近障碍物的最近距离）。设置为负值可禁用 _防撞功能_。<br>> **Warning** 此值为距离传感器的距离，而非机体或螺旋桨的外部。务必预留安全余量！ |
| <a id="CP_DELAY"></a>[CP_DELAY](../advanced_config/parameter_reference.md#CP_DELAY)                | 设置传感器和速度设定点跟踪延迟。参见下方的[延迟调节](#delay_tuning)。                                                                                                                                                                                                   |
| <a id="CP_GUIDE_ANG"></a>[CP_GUIDE_ANG](../advanced_config/parameter_reference.md#CP_GUIDE_ANG)    | 设置（相对于指令方向两侧）的允许偏离角度，当在该方向发现更少障碍物时，机体可在此角度范围内偏离。参见下方的[引导调节](#angle_change_tuning)。                                                                                                 |
| <a id="CP_GO_NO_DATA"></a>[CP_GO_NO_DATA](../advanced_config/parameter_reference.md#CP_GO_NO_DATA) | 设置为 1 时允许机体在无传感器覆盖的区域移动（默认为 0/`False`）。                                                                                                                                                                                   |
| <a id="MPC_POS_MODE"></a>[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)    | 必须设置为 `Acceleration based`。                                                                                                                                                                                                                                                           |## 算法描述

所有传感器的数据都会融合到机体周围36个扇区的内部表示中，每个扇区包含传感器数据及最后观测时间的信息，或指示该扇区无数据可用。
当机体被指令向特定方向移动时，会检查该方向半球内所有扇区，判断移动是否会将机体带近障碍物。
如果会，机体速度将受到限制。

这种速度限制同时考虑了由[MPC_XY_P](../advanced_config/parameter_reference.md#MPC_XY_P)调节的内部速度环，以及通过[MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX)和[MPC_ACC_HOR](../advanced_config/parameter_reference.md#MPC_ACC_HOR)的加加速度最优速度控制器。
速度会被限制以确保机体能及时停止，从而维持[CP_DIST](#CP_DIST)参数指定的距离。
每个扇区的传感器范围也会被考虑，通过相同机制限制速度。

::: info
如果某个方向没有传感器数据，该方向的速度将被限制为0（防止机体撞向不可见物体）。
若希望自由移动到无传感器覆盖的方向，可通过设置[CP_GO_NO_DATA](#CP_GO_NO_DATA)=1启用该功能。
:::

延迟（包括机体速度跟踪设定值和接收外部传感器数据的延迟）通过[CP_DELAY](#CP_DELAY)参数保守估计。
该参数应根据具体机体进行[调参](#delay_tuning)。

如果相邻扇区的"质量"显著优于指令扇区，请求输入的方向最多可修改到[CP_GUIDE_ANG](#CP_GUIDE_ANG)参数指定的角度。
这有助于微调用户输入，引导机体绕过障碍物而非卡住。

### 范围数据丢失

如果自动驾驶仪在0.5秒内未接收到任何传感器的范围数据，将输出警告_No range data received, no movement allowed_。
这将强制xy平面的速度设定值归零。
在5秒内未接收到任何数据后，机体将切换到[ HOLD模式](../flight_modes_mc/hold.md)。
若希望机体再次移动，需通过将参数[CP_DIST](#CP_DIST)设为负值，或切换到非[Position模式](../flight_modes_mc/position.md)（例如切换到_ Altitude模式_或_ Stabilized模式_）来禁用防撞功能。

如果连接了多个传感器且其中一个断开连接，只要仍在其他传感器的视野范围内（FOV）仍可飞行。
故障传感器的数据将过期，该传感器覆盖的区域将被视为未覆盖区域，因此无法移动到该区域。

:::warning
启用[CP_GO_NO_DATA=1](#CP_GO_NO_DATA)时需谨慎，该功能允许机体飞出传感器覆盖区域。
如果多个传感器中的一个断开连接，该传感器覆盖区域将被视为未覆盖区域，此时可不受约束地移动到该区域。
:::

### CP_DELAY延迟调参 {#delay_tuning}

需要考虑的两个主要延迟来源是：_传感器延迟_和机体_速度设定值跟踪延迟_。
这两个延迟源均通过[CP_DELAY](#CP_DELAY)参数调参。

直接连接到飞行控制器的距离传感器的_传感器延迟_可假设为0。
对于外部视觉系统，传感器延迟可能高达0.2秒。

机体_速度设定值跟踪延迟_可通过在[Position模式](../flight_modes_mc/position.md)下全速飞行后执行停止指令进行测量。
通过日志可测量实际速度与速度设定值之间的延迟。
跟踪延迟通常在0.1到0.5秒之间，具体取决于机体尺寸和调参。

:::tip
如果机体接近障碍物时速度出现振荡（即减速-加速-减速），则延迟设置过高。
:::

### CP_GUIDE_ANG引导调参 {#angle_change_tuning}

根据机体类型、环境类型和操作员技能水平，可能需要不同程度的引导。
将[CP_GUIDE_ANG](#CP_GUIDE_ANG)参数设为0将禁用引导功能，此时机体仅严格按指令方向移动。
增大该参数值可使机体自主选择最优方向避障，从而更容易通过狭窄间隙，并在绕行时精确保持最小距离。

如果该参数过小，机体在靠近障碍物时可能感觉"卡住"，因为仅允许在最小距离处远离障碍物的移动。
如果参数过大，机体可能会朝操作员未指令的方向滑离障碍物。
根据测试，30度是一个良好的平衡点，但不同机体可能有不同需求。

::: info
引导功能永远不会将机体导向无传感器数据的方向。
如果单个向前距离传感器的机体感觉卡住，这很可能是因为引导功能因信息不足而无法安全调整方向。
:::

## 算法描述

所有传感器的数据被融合为机体周围72个扇区的内部表示，每个扇区包含传感器数据及其最后观测时间信息，或表示该扇区无可用数据。
当机体被指令向特定方向移动时，该方向半球内的所有扇区将被检查，判断移动是否会将机体带入距离障碍物过近的范围。如果存在此风险，机体速度将受到限制。

该算法可分为两部分：对操作员设定的加速度目标的约束，以及对机体当前速度的补偿。

::: info
如果某个方向没有传感器数据，该方向的移动将被限制为0（防止机体撞向未被观测的物体）。
若希望自由移动至无传感器覆盖的方向，可通过将[CP_GO_NO_DATA](#CP_GO_NO_DATA)设置为1启用此功能。
:::

### 加速度约束

我们将加速度设定值分解为两个分量：一个平行于障碍物最近距离，另一个垂直于该距离。然后根据下图对这两个分量进行缩放：

![Scalefactor](../../assets/computer_vision/collision_prevention/scalefactor.png)

 <!-- 该图的代码位于本文件末尾 -->

### 速度补偿

该速度限制通过[jerk最优速度控制器](../config_mc/mc_jerk_limited_type_trajectory.md)结合[MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX)和[MPC_ACC_HOR](../advanced_config/parameter_reference.md#MPC_ACC_HOR)实现。其中 
当前速度与最大允许速度进行比较，确保我们仍能基于最大允许jerk、加速度和延迟进行制动。通过此方式，我们可以使用加速度控制器的比例增益（[MPC_XY_VEL_P_ACC](../advanced_config/parameter_reference.md#MPC_XY_VEL_P_ACC)）将其转换为加速度。

### 延迟

与防撞相关的延迟（包括机体跟踪速度设定值的延迟和接收外部传感器数据的延迟），通过[CP_DELAY](#CP_DELAY)参数进行保守估计。
此参数应根据具体机体进行[tuned](#delay_tuning)。

## 伴飞设备设置 {#companion}

::: warning
当前伴飞设备的实现/设置未经测试（原始伴飞项目已停止维护并归档）。
:::

如果使用伴飞计算机或外部传感器，则需要提供一连串的[OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE)消息，这些消息应反映检测到障碍物的时间和位置。

消息必须发送的最低频率取决于机体速度 - 在更高频率下，机体将有更长时间响应检测到的障碍物。该系统的初步测试使用了以4 m/s速度移动的机体，并以10Hz的频率发出`OBSTACLE_DISTANCE`消息（这是视觉系统支持的最大频率）。系统可能在显著更高的速度和更低的频率下距离更新时仍能良好工作。

## Gazebo模拟

_Collision Prevention_ 功能可以通过 [Gazebo](../sim_gazebo_gz/index.md) 使用 [x500_lidar_2d](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-2d-lidar) 模型进行测试。  
为此，请运行以下命令启动带有x500激光雷达模型的模拟：

```sh
make px4_sitl gz_x500_lidar_2d
```

接下来，调整相关参数到合适的数值，并在模拟世界中添加任意障碍物，以测试防撞功能。

下图展示了在Gazebo中查看的防撞模拟效果。

![在Gazebo中使用x500_lidar_2d模型的碰撞检测RViz图像](../../assets/simulation/gazebo/vehicles/x500_lidar_2d_viz.png)

## 开发信息/工具

### 使用 PlotJuggler 实时绘制障碍物距离和最小距离

[PlotJuggler](../log/plotjuggler_log_analysis.md) 可用于实时绘制障碍物距离的监测与可视化，包括到最近障碍物的最小距离。

<lite-youtube videoid="amLheoHgwc4" title="Plotting Obstacle Distance and Minimum Distance in Real-Time with PlotJuggler"/>

要使用此功能，您需要在 PlotJuggler 中添加一个响应式 Lua 脚本，并配置 PX4 以导出 [`obstacle_distance_fused`](../msg_docs/ObstacleDistance.md) UORB 主题数据。Lua 脚本通过在每个时间步提取 `obstacle_distance_fused` 数据，将距离值转换为笛卡尔坐标，并将其推送到 PlotJuggler。

步骤如下：

1. 按照 [PlotJuggler 实时绘制 uORB 主题数据](../debug/plotting_realtime_uorb_data.md) 中的说明操作。
2. 配置 PX4 以发布障碍物距离数据（以便 PlotJuggler 可用）：

   将 [`obstacle_distance_fused`](../msg_docs/ObstacleDistance.md) UORB 主题添加到您的 [`dds_topics.yaml`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 中，以便 PX4 发布该主题：

   ```sh
   - topic: /fmu/out/obstacle_distance_fused
     type: px4_msgs::msg::ObstacleDistance
   ```

   更多信息请参见 [uXRCE-DDS](../middleware/uxrce_dds.md) 中的 [DDS Topics YAML](../middleware/uxrce_dds.md#dds-topics-yaml)（PX4-ROS 2/DDS 桥接）。

3. 打开 PlotJuggler，导航到 **工具 > 响应式脚本编辑器** 部分。
   在 **脚本编辑器** 标签页中，将以下脚本添加到相应部分：

   - **全局代码，执行一次：**

     ```lua
     obs_dist_fused_xy = ScatterXY.new("obstacle_distance_fused_xy")
     obs_dist_min = Timeseries.new("obstacle_distance_minimum")
     ```

   - **function(tracker_time)**

     ```lua
     obs_dist_fused_xy:clear()

     i = 0
     angle_offset = TimeseriesView.find("/fmu/out/obstacle_distance_fused/angle_offset")
     increment = TimeseriesView.find("/fmu/out/obstacle_distance_fused/increment")
     min_dist = 65535

     -- 在 tracker_time 处缓存 increment 和 angle_offset 值，避免重复调用
     local angle_offset_value = angle_offset:atTime(tracker_time)
     local increment_value = increment:atTime(tracker_time)

     if increment_value == nil or increment_value <= 0 then
         print("Invalid increment value: " .. tostring(increment_value))
         return
     end

     local max_steps = math.floor(360 / increment_value)

     while i < max_steps do
         local str = string.format("/fmu/out/obstacle_distance_fused/distances[%d]", i)
         local distance = TimeseriesView.find(str)
         if distance == nil then
             print("No distance data for: " .. str)
             break
         end

         local dist = distance:atTime(tracker_time)
         if dist ~= nil and dist < 65535 then
             -- 计算角度和笛卡尔坐标
             local angle = angle_offset_value + i * increment_value
             local y = dist * math.cos(math.rad(angle))
             local x = dist * math.sin(math.rad(angle))

             obs_dist_fused_xy:push_back(x, y)

             -- 更新最小距离
             if dist < min_dist then
                 min_dist = dist
             end
         end

         i = i + 1
     end

     -- 循环结束后推送最小距离
     if min_dist < 65535 then
         obs_dist_min:push_back(tracker_time, min_dist)
     else
         print("No valid minimum distance found")
     end
     ```

4. 在右上角输入脚本名称，点击 **保存**。
   保存后，脚本应出现在 _活动脚本_ 部分。
5. 按照 [PlotJuggler 实时绘制 uORB 主题数据](../debug/plotting_realtime_uorb_data.md) 中的描述开始数据流传输。
   您应在左侧看到 `obstacle_distance_fused_xy` 和 `obstacle_distance_minimum` 时间序列。

注意，加载新日志文件或清除数据后，需要再次点击 **保存** 以重新启用脚本。

### 传感器数据概览

防撞功能具有一个内部障碍物距离地图，将机体周围环境划分为 360 度的扇区进行监测。

1. **旋转激光雷达 (Rotary Lidar)**  
   用于检测周围环境的障碍物距离，数据通过 `obstacle_distance` 主题发布。

2. **测距传感器 (Rangefinder)**  
   提供垂直方向的障碍物距离信息，适用于悬停或低速飞行场景。

3. **伴飞计算机 (Companion Computers)**  
   通过 MAVLink 协议与 PX4 通信，实现复杂避障算法和路径规划。

**配置要点：**
- 确保所有传感器数据在 `vehicle_status` 处于 `PX4_VEHICLE_STATUS_ACTIVE` 状态时同步。
- 使用 `sensor_offset` 参数校正多传感器数据的空间对齐。
- 启用 `obstacle_distance_fusion` 以融合多源数据，提高检测精度。

**注意事项：**
- 避障区域默认半径为 5 米，可通过 `obstacle_distance_radius` 调整。
- 在 GPS 信号弱的环境中，建议启用 `obstacle_distance_visual_inertial` 以增强定位稳定性。
- 防撞算法优先级可通过 `obstacle_distance_priority` 参数自定义。

::: info 四元数说明
四元数 `q` 仅在 `orientation` 设置为 `ROTATION_CUSTOM` 时使用，用于描述传感器坐标系相对于机体坐标系的旋转。
:::

```python
# 示例：使用 NumPy 处理障碍物距离数据
import numpy as np

def process_obstacle_data(obstacle_distances):
    valid_distances = obstacle_distances[obstacle_distances < 65535]
    if len(valid_distances) > 0:
        min_distance = np.min(valid_distances)
        return min_distance
    else:
        return np.inf
```