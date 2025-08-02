

# 防撞

_防撞_ 功能可用于在机体撞击障碍物前自动减速并停止。
当使用基于加速度的 [Position mode](../flight_modes_mc/position.md) 时，可为多旋翼机体启用该功能（或VTOL机体在多旋翼模式下）。

多旋翼机体在 [Position mode](../flight_modes_mc/position.md) 下（将 [MPC_POS_MODE](#MPC_POS_MODE) 设置为 `Acceleration based`）可启用该功能，可使用来自外部伴飞计算机、通过MAVLink连接的外部测距仪、连接到飞控的测距仪，或上述任意组合的传感器数据。

如果传感器范围不够大！防撞功能可能会限制机体最大速度
它还会阻止在没有传感器数据的方向上移动（即如果你没有后方传感器数据，将无法向后飞行）。

:::tip
如果高速飞行至关重要，建议在不需要时禁用防撞功能。
:::

:::tip
请确保在所有想要飞行的方向上都配备了传感器/传感器数据（防撞功能启用时）。
:::

## 概述

机体限制当前速度以在接近障碍物时减速，并调整加速度设定值以避免碰撞轨迹。  
要远离（或平行于）障碍物，用户必须命令机体移动到不会使其接近障碍物的设定点。  
如果在请求的设定点两侧的固定范围内存在“更优”的设定点，算法将对设定点方向进行微调。

当防撞功能正在主动控制速度设定值时，用户会通过_QGroundControl_收到通知。  

PX4软件配置将在下一节介绍。  
如果使用连接到飞控器的距离传感器进行防撞，需要按照[PX4距离传感器](#rangefinder)的说明进行连接和配置。  
如果使用伴飞计算机提供障碍物信息，请参见下方的[伴飞计算机设置](#companion)。

## 支持的测距传感器 {#rangefinder}

### Lanbao PSK-CM8JL65-CC5 [已停产]

At time of writing PX4 allows you to use the [Lanbao PSK-CM8JL65-CC5](../sensor/cm8jl65_ir_distance_sensor.md) IR distance sensor for collision prevention "out of the box", with minimal additional configuration:

- First [attach and configure the sensor](../sensor/cm8jl65_ir_distance_sensor.md), and enable collision prevention (as described above, using [CP_DIST](#CP_DIST)).
- Set the sensor orientation using [SENS_CM8JL65_R_0](../advanced_config/parameter_reference.md#SENS_CM8JL65_R_0).

### LightWare LiDAR SF45 旋转激光雷达

PX4 v1.14（及更高版本）支持 [LightWare LiDAR SF45](../sensor/sf45_rotating_lidar.md) 旋转激光雷达，该设备提供320度的感知范围。

### 其他测距传感器

其他传感器可通过修改驱动代码来启用，包括设置传感器方向和视场角。

- 在特定端口上安装并配置距离传感器（参见[sensor-specific docs](../sensor/rangefinders.md)），并使用[CP_DIST](#CP_DIST)启用防撞功能。
- 修改驱动代码以设置方向。  
  此操作应通过模仿 `SENS_CM8JL65_R_0` 参数实现（你也可以在传感器的 _module.yaml_ 文件中硬编码方向，例如 `sf0x start -d ${SERIAL_DEV} -R 25` - 其中 25 等效于 `ROTATION_DOWNWARD_FACING`）。
- 修改驱动代码以在距离传感器 UORB 主题中设置 _field of view_（`distance_sensor_s.h_fov`）。

## PX4 (软件) 设置

通过在 _QGroundControl_ 中[设置以下参数](../advanced_config/parameters.md)来配置防撞功能：

| 参数                                                                                          | 描述                                                                                                                                                                                                                                                                                     |
| -------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="CP_DIST"></a>[CP_DIST](../advanced_config/parameter_reference.md#CP_DIST)                   | 设置允许的最小距离（机体可接近障碍物的最近距离）。设为负值可禁用_防撞功能_。<br>> **Warning** 该值是距离传感器的距离，而不是机体或螺旋桨的外侧距离。请务必留出安全余量！ |
| <a id="CP_DELAY"></a>[CP_DELAY](../advanced_config/parameter_reference.md#CP_DELAY)                | 设置传感器和速度设定点跟踪延迟。参见下方的[延迟调节](#delay_tuning)。                                                                                                                                                                                                   |
| <a id="CP_GUIDE_ANG"></a>[CP_GUIDE_ANG](../advanced_config/parameter_reference.md#CP_GUIDE_ANG)    | 设置角度（在指令方向的两侧），当发现该方向障碍物较少时，机体可在此角度范围内偏离。参见下方的[引导调节](#angle_change_tuning)。                                                                                                 |
| <a id="CP_GO_NO_DATA"></a>[CP_GO_NO_DATA](../advanced_config/parameter_reference.md#CP_GO_NO_DATA) | 设置为 1 以允许机体向无传感器覆盖的方向移动（默认为 0/`False`）。                                                                                                                                                                                   |
| <a id="MPC_POS_MODE"></a>[MPC_POS_MODE](../advanced_config/parameter_reference.md#MPC_POS_MODE)    | 必须设置为 `Acceleration based`。                                                                                                                                                                                                                                                           |

## 算法描述

机体周围所有传感器的数据被融合为36个扇区的内部表示，每个扇区包含传感器数据及其最后观测时间信息，或表示该扇区无可用数据的提示。  
当机体被指令向特定方向移动时，该方向半球内的所有扇区会被检查以判断移动是否会将机体带入接近障碍物的区域。若存在此类风险，则限制机体速度。

该速度限制同时考虑了由[MPC_XY_P](../advanced_config/parameter_reference.md#MPC_XY_P)调节的内环速度环，以及通过[MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX)和[MPC_ACC_HOR](../advanced_config/parameter_reference.md#MPC_ACC_HOR)实现的[jerk-optimal velocity controller](../config_mc/mc_jerk_limited_type_trajectory.md)。速度被限制为确保机体能在规定距离内及时停止（由[CP_DIST](#CP_DIST)指定）。同时，每个扇区传感器的探测范围也会通过相同机制限制速度。

::: info
若某方向无传感器数据，则该方向的速度会被限制为0（防止机体撞击未检测到的物体）。  
若希望向无传感器覆盖的方向自由移动，可通过将[CP_GO_NO_DATA](#CP_GO_NO_DATA)设为1实现。
:::

通过[CP_DELAY](#CP_DELAY)参数保守估计机体跟踪速度设定值的延迟和接收外部传感器数据的延迟。该参数需针对特定机体进行[tuned](#delay_tuning)。

若相邻扇区的路径明显更优，请求输入的方向可通过最大[CP_GUIDE_ANG](#CP_GUIDE_ANG)角度进行修正。这有助于微调用户输入，使机体绕过障碍物而非卡在障碍物上。

### 距离数据丢失

若自动驾驶仪在0.5秒内未接收到任何传感器的距离数据，将输出警告：_No range data received, no movement allowed_（未接收到距离数据，不允许移动）。  
此警告将强制xy方向的速度设定值归零。  
若在5秒内未接收到任何数据，机体将切换到[HOLD模式](../flight_modes_mc/hold.md)。  
若需恢复机体移动功能，可通过两种方式禁用防撞保护：  
1. 将参数[CP_DIST](#CP_DIST)设置为负值  
2. 切换至除[Position模式](../flight_modes_mc/position.md)外的其他模式（如"Altitude模式"或"Stabilized模式"）

若连接了多个传感器且其中一个传感器失联，只要其他传感器的视场角（FOV）仍能覆盖飞行区域，仍可正常飞行。  
故障传感器的数据将被标记为过期，其覆盖区域将被视为无数据区域，此时该区域将被禁止移动。

:::warning
启用[CP_GO_NO_DATA=1](#CP_GO_NO_DATA)时需谨慎，该参数允许机体飞出传感器覆盖区域。  
当多个传感器中某一个失联时，该传感器覆盖区域将被视为无数据区域，此时可不受限制地移动到该区域。
:::

### CP_DELAY 延迟调整 {#delay_tuning}

需要考虑两个主要的延迟来源：_传感器延迟_ 和 机体 _速度设定点跟踪延迟_。
这两个延迟来源都通过 [CP_DELAY](#CP_DELAY) 参数进行调整。

对于直接连接到飞控的测距传感器，其 _传感器延迟_ 可以假设为 0。
对于外部视觉系统，其传感器延迟可能高达 0.2 秒。

机体 _速度设定点跟踪延迟_ 可以通过在 [Position mode](../flight_modes_mc/position.md) 下以最高速度飞行并发出停止指令来测量。
实际速度和速度设定点之间的延迟可以通过飞控日志进行测量。
跟踪延迟通常在 0.1 到 0.5 秒之间，具体取决于机体尺寸和调参情况。

:::tip
如果机体在接近障碍物时速度出现振荡（即减速、加速、再减速），则说明延迟设置过高。
:::

### CP_GUIDE_ANG 引导调优 {#angle_change_tuning}

根据机体类型、环境类型和操作员技能的不同，所需的引导量可能不同。  
将[CP_GUIDE_ANG](#CP_GUIDE_ANG)参数设置为0将禁用引导，导致机体仅严格按照指令方向移动。  
增大该参数值将使机体选择最佳方向以避开障碍物，从而更容易穿过狭窄间隙，并在绕过物体时保持精确的最小距离。

如果该参数过小，机体在靠近障碍物时可能会感觉"卡住"，因为只允许在最小距离处远离障碍物的移动。  
如果参数过大，机体可能会感觉在操作员未指令的方向上滑开。  
通过测试发现，30度是一个良好的平衡点，尽管不同机体可能有不同需求。

::: info
引导功能永远不会在没有传感器数据的情况下指挥机体。  
如果机体仅有一个向前的测距传感器时感觉卡住，这可能是因为引导功能因信息不足而无法安全调整方向。
:::

## 算法描述

所有传感器的数据都会融合到机体周围72个扇区的内部表示中，每个扇区包含传感器数据及其上次观察时间信息，或者表示该扇区无可用数据。  
当机体被指令向特定方向移动时，该方向半球内的所有扇区会被检查，以确认移动是否会将机体带入到障碍物过近的范围。如果是，机体速度将受到限制。

该算法可分为两个部分：对操作员设定的加速度设定值进行约束，以及对机体当前速度进行补偿。

::: info
如果某个方向没有传感器数据，该方向的移动将被限制为0（防止机体撞向不可见物体）。  
如果希望在无传感器覆盖的方向自由移动，可通过将 [CP_GO_NO_DATA](#CP_GO_NO_DATA) 设置为1来启用此功能。
:::

### 加速度限制

为此我们将加速度设定值分解为两个分量：一个与障碍物最近距离方向平行，另一个垂直于该方向。然后根据下图对这两个分量分别进行缩放处理。

![缩放系数](../../assets/computer_vision/collision_prevention/scalefactor.png)

 <!-- the code for this figure is at the end of this file -->

### 速度补偿

该速度限制通过 [MPC_JERK_MAX](../advanced_config/parameter_reference.md#MPC_JERK_MAX) 和 [MPC_ACC_HOR](../advanced_config/parameter_reference.md#MPC_ACC_HOR) 考虑了 [jerk-optimal velocity controller](../config_mc/mc_jerk_limited_type_trajectory.md)。其中 <!--this is only partially valid anymore... check -->
当前速度与最大允许速度进行比较，以确保我们仍能基于最大允许的加加速度（jerk）、加速度和延迟进行减速。通过这种方式，我们可以利用加速度控制器的比例增益（[MPC_XY_VEL_P_ACC](../advanced_config/parameter_reference.md#MPC_XY_VEL_P_ACC)）将其转换为加速度。

### 延迟

与防撞相关的延迟（包括机体跟踪速度设定点和接收外部来源传感器数据的延迟）通过[CP_DELAY](#CP_DELAY)参数进行保守估计。
该参数应根据特定机体进行[tuned](#delay_tuning)调节。

## 伴生设备设置 {#companion}

::: warning
伴生设备的实现/设置目前未经测试（原始的伴生项目已不再维护并已归档）。
:::

如果使用伴生计算机或外部传感器，则需要提供一连串的[OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE)消息，这些消息应反映障碍物被检测到的时间和位置。

消息发送的最小速率取决于机体速度——在更高频率下，机体将有更长的响应时间来应对检测到的障碍物。
系统初期测试使用了一架以4 m/s速度移动的机体，`OBSTACLE_DISTANCE`消息以10Hz的频率发出（这是视觉系统支持的最高速率）。
系统在显著更高的速度和更低频率的距离更新下可能仍能良好运行。

## Gazebo仿真

[防撞]可以通过使用[Gazebo](../sim_gazebo_gz/index.md)和[x500_lidar_2d](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-2d-lidar)模型进行测试。  
要进行此操作，请运行以下命令启动x500激光雷达模型的仿真：

```sh
make px4_sitl gz_x500_lidar_2d
```

接下来，调整相关参数到适当值，并在仿真世界中添加任意障碍物以测试防撞功能。  
下图展示了在Gazebo中查看的防撞仿真。

![使用x500_lidar_2d模型在Gazebo中的碰撞检测的RViz图像](../../assets/simulation/gazebo/vehicles/x500_lidar_2d_viz.png)

## 开发信息/工具

### 使用 PlotJuggler 实时绘制障碍物距离和最小距离

[PlotJuggler](../log/plotjuggler_log_analysis.md) 可以用于实时监控和可视化障碍物距离，包括到最近障碍物的最小距离。

<lite-youtube videoid="amLheoHgwc4" title="Plotting Obstacle Distance and Minimum Distance in Real-Time with PlotJuggler"/>

要使用此功能，需要向 PlotJuggler 添加一个响应式 Lua 脚本，并配置 PX4 以导出 [`obstacle_distance_fused`](../msg_docs/ObstacleDistance.md) UORB 主题数据。  
Lua 脚本通过在每个时间步提取 `obstacle_distance_fused` 数据，将距离值转换为笛卡尔坐标，并推送到 PlotJuggler。

步骤如下：

1. 按照 [PlotJuggler 实时绘制 uORB 主题数据](../debug/plotting_realtime_uorb_data.md) 中的说明操作  
2. 配置 PX4 以发布障碍物距离数据（使其可用于 PlotJuggler）：

   将 [`obstacle_distance_fused`](../msg_docs/ObstacleDistance.md) UORB 主题添加到您的 [`dds_topics.yaml`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_1dd_client/dds_topics.yaml)，以便由 PX4 发布：

   ```sh
   - topic: /fmu/out/obstacle_distance_fused
     type: px4_msgs::msg::ObstacleDistance
   ```

   更多信息请参见 [uXRCE-DDS](../middleware/uxrce_dds.md) 中的 [DDS Topics YAML](../middleware/uxrce_dds.md#dds-topics-yaml) (PX4-ROS 2/DDS 桥接)

3. 打开 PlotJuggler 并导航到 **工具 > 响应式脚本编辑器** 部分。  
   在 **脚本编辑器** 标签页中，将以下脚本添加到相应部分：

   - **全局代码，仅执行一次：**

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

     -- 在 tracker_time 缓存 increment 和 angle_offset 值以避免重复调用
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
   保存后，脚本应出现在 **活动脚本** 部分。  
5. 按照 [PlotJuggler 实时绘制 uORB 主题数据](../debug/plotting_realtime_uorb_data.md) 中描述的方法开始数据流传输。  
   左侧应显示 `obstacle_distance_fused_xy` 和 `obstacle_distance_minimum` 时间序列。

注意：加载新日志文件或清除数据后，需要再次点击 **保存** 以重新启用脚本。

### 传感器数据概览

防撞功能具有一个内部障碍物距离图，该地图将无人机周围平面划分为72个扇区。
这些信息在内部存储于 [`obstacle_distance`](../msg_docs/ObstacleDistance.md) UORB 主题中。
新的传感器数据会与现有地图进行比较，并用于更新所有已发生改变的区域。

`obstacle_distance` 主题中的角度定义如下：

![Obstacle_Distance Angles](../../assets/computer_vision/collision_prevention/obstacle_distance_def.svg)

来自测距仪、旋转激光雷达或伴飞计算机的数据会以不同的方式处理，如下所述。

#### 旋转激光雷达

旋转激光雷达直接将其数据添加到 [`obstacle_distance`](../msg_docs/ObstacleDistance.md) uORB 主题。

#### 测距仪

测距仪将其数据发布到 [`distance_sensor`](../msg_docs/DistanceSensor.md) uORB 主题。

然后这些数据会被映射到 `obstacle_distance` 主题。
所有与测距仪的方向（`orientation` 和 `q`）以及水平视场角（`h_fov`）存在重叠的扇区都会被赋予该测量值。
例如，一个测量范围为9.99°到10.01°的距离传感器，其测量值会分配给5°和10°对应扇区，覆盖从2.5°到12.5°的弧度范围。

::: info
四元数 `q` 仅在 `orientation` 设置为 `ROTATION_CUSTOM` 时使用。
:::

#### 伴随计算机

伴随计算机通过ROS2或[OBSTACLE_DISTANCE](https://mavlink.io/en/messages/common.html#OBSTACLE_DISTANCE) MAVLink消息更新`obstacle_distance`主题。

<!-- 要编辑此图像，请在inkscape中打开 -->
<!-- 生成缩放因子图的代码
import numpy as np
import matplotlib.pyplot as plt
obstacle_dist = -5
cp_dist = 0
scale_dist = 10
x_values_1 = np.linspace(obstacle_dist, cp_dist, 100)  # 段1：obstacle到cp_dist
x_values_2 = np.linspace(cp_dist, scale_dist, 100)  # 段2：cp_dist到scale_dist
x_values_3 = np.linspace(scale_dist, 15, 100)  # 段3：scale_dist之后
def acceleration_setpoint_1(x):
  return -1 + (x - obstacle_dist) / (cp_dist - obstacle_dist)
def acceleration_setpoint_2(x):
  return ((x - cp_dist) / (scale_dist - cp_dist))**2
def acceleration_setpoint_3(x):
  return 1
y_values_1 = [acceleration_setpoint_1(x) for x in x_values_1]
y_values_2 = [acceleration_setpoint_2(x) for x in x_values_2]
y_values_3 = [acceleration_setpoint_3(x) for x in x_values_3]
plt.figure(figsize=(15, 5))
plt.plot(x_values_1, y_values_1, color='red', label="Below Zero", linewidth=4)
plt.plot(x_values_2, y_values_2, color='orange', label="Above Zero", linewidth=4)
plt.plot(x_values_3, y_values_3, color='green', label="Above Scale Distance", linewidth=4)
plt.xlabel("Distance")
plt.yticks([-1, 0, 1], ['-1', '0', '1'])  # 在-1、0和1处设置刻度
plt.ylabel("Scalefactor")  # 将y轴标签改为"Scale"
plt.title("Manual Acceleration Setpoint Scaling")
plt.xticks([obstacle_dist, cp_dist, scale_dist], ['Obstacle', 'CP_DIST', 'scale_distance = MPC_VEL_MANUAL / MPC_XY_P'])
plt.grid(True)
plt.show() -->