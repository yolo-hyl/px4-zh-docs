# 距离传感器（测距仪）

距离传感器提供距离测量数据，可用于[地形跟随](../flying/terrain_following_holding.md#terrain_following)、[地形保持](../flying/terrain_following_holding.md#terrain_hold)（即摄影用精确悬停）、改进着陆行为（[条件范围辅助](../advanced_config/tuning_the_ecl_ekf.md#conditional-range-aiding)）、高度限制预警、防撞等。

本节列出了PX4支持的距离传感器（链接至更详细文档），并提供所有测距仪所需的[通用配置](#configuration)、[测试](#testing)以及在[Gazebo](#gazebo-simulation)或[Gazebo-Classic](#gazebo-classic-simulation)中的模拟信息。更详细的设置和配置信息请参见下方链接的主题（及侧边栏）。

<img src="../../assets/hardware/sensors/lidar_lite/lidar_lite_v3.jpg" alt="Lidar Lite V3" width="200px" /><img src="../../assets/hardware/sensors/lidar_lightware/sf11c_120_m.jpg" alt="LightWare SF11/C Lidar" width="200px" /><img src="../../assets/hardware/sensors/optical_flow/ark_flow_distance_sensor.jpg" alt="ARK Flow" width="200px">## 支持的测距仪

::: tip
这是可以与PX4配合使用的测距仪子集。
[模块: 距离传感器](../modules/modules_driver_distance_sensor.md)列出了其他非CAN测距仪的PX4驱动程序。
此外还可能存在未在此列出的DroneCAN测距仪。
:::

### ARK Flow & AKR Flow MR

[ARK Flow](../dronecan/ark_flow.md)和[ARK Flow MR](../dronecan/ark_flow_mr.md)是开源的飞行时间（ToF）和光流传感器模块，分别能够测量8cm至30m和8cm至50m的范围。
它可以通过CAN1端口连接到飞控器，并通过CAN2端口连接其他传感器。
它支持[DroneCAN](../dronecan/index.md)，运行[PX4 DroneCAN固件](../dronecan/px4_cannode_fw.md)，并采用紧凑的外形设计。

### Holybro ST VL53L1X 激光雷达

[VL53L1X](https://holybro.com/products/st-vl53l1x-lidar)是先进的飞行时间（ToF）激光测距传感器，属于ST FlightSense™产品家族的升级版。
这是市场上最快的微型ToF传感器，测距精度可达4米，测距频率最高可达50Hz。

它配有JST GHR 4针连接器，兼容[Pixhawk 4](../flight_controller/pixhawk4.md)、[Pixhawk 5X](../flight_controller/pixhawk5x.md)及其他遵循[Pixhawk连接器标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)的飞控器的I2C端口。

### Lidar-Lite

[Lidar-Lite](../sensor/lidar_lite.md)是一款紧凑型高性能光学测距传感器。
其测距范围为5cm-40m，可通过PWM或I2C端口连接。

### MaxBotix I2CXL-MaxSonar-EZ

MaxBotix [I2CXL-MaxSonar-EZ](https://www.maxbotix.com/product-category/i2cxl-maxsonar-ez-products)系列包含多种短距离超声波测距仪，适用于起降辅助和防撞。
这些设备可通过I2C端口连接。

测距仪通过参数 [SENS_EN_MB12XX](../advanced_config/parameter_reference.md#SENS_EN_MB12XX) 启用。

### Lightware LIDARs

[Lightware SFxx激光雷达](../sensor/sfxx_lidar.md)提供一系列轻量级"激光高度计"，适用于多种无人机应用。

PX4支持：SF11/c和SF/LW20。
PX4还可与以下停产型号配合使用：SF02、SF10/a、SF10/b、SF10/c。

其他型号可通过下文描述的[RaccoonLab Cyphal和DroneCAN测距仪适配器](#raccoonlab-cyphal-and-dronecan-rangefinder-adapter)支持。

PX4还支持[LightWare LiDAR SF45旋转激光雷达](../sensor/sf45_rotating_lidar.md)，用于[防撞](../computer_vision/collision_prevention.md)应用。

### TeraRanger 测距仪

[TeraRanger](../sensor/teraranger.md)提供基于红外飞行时间（ToF）技术的轻量级测距传感器。
它们通常比超声波更快且测距范围更大，比激光系统更小更轻。

PX4支持通过I2C总线连接的以下型号：TeraRanger One、TeraRanger Evo 60m和TeraRanger Evo 600Hz。

### Ainstein US-D1 标准雷达高度计

_Ainstein_ [US-D1 标准雷达高度计](../sensor/ulanding_radar.md)是一款专为无人机优化的紧凑型微波测距仪。
其测距范围约为50米。
该产品的主要优势是可以在各种天气条件下和所有地形类型（包括水面）上有效工作。

### LeddarOne

[LeddarOne](../sensor/leddar_one.md)是一款小尺寸激光雷达模块，具有窄而分散的光束，在坚固可靠的封装中提供出色的检测范围和性能。
其测距范围为1cm至40m，需要连接到UART/串口总线。

### TFmini

[Benewake TFmini激光雷达](../sensor/tfmini.md)是一款微型、低成本、低功耗的激光雷达，测距范围为12米。

### PSK-CM8JL65-CC5

<Badge type="info" text="已停产" />

[Lanbao PSK-CM8JL65-CC5 飞行时间红外测距传感器](../sensor/cm8jl65_ir_distance_sensor.md)是一款非常小巧（38 mm x 18mm x 7mm，<10g）的红外测距传感器，测距范围为0.17m-8m，具有毫米级精度。
必须连接到UART/串口总线。

### Avionics Anonymous UAVCAN 激光高度计接口

[Avionics Anonymous UAVCAN 激光高度计接口](../dronecan/avanon_laser_interface.md)允许通过[DroneCAN](../dronecan/index.md)（比I2C更稳健的接口）将多种常见测距仪（如[Lightware SF11/c, SF30/D](../sensor/sfxx_lidar.md)等）连接到[CAN](../can/index.md)总线。

### RaccoonLab Cyphal和DroneCAN测距仪适配器

[RaccoonLab Cyphal和DroneCAN测距仪适配器](https://raccoonlab.co/tproduct/360882105-910084093051-cyphal-and-dronecan-rangefinder-adapter)允许通过Cyphal或DroneCAN将多种常见测距仪连接到CAN总线，相比I2C或UART提供更稳健的接口。
该适配器通过I2C或UART读取测量数据，并以米为单位发布范围数据，是无人机、机器人和技术文档应用的多功能解决方案。

支持的测距仪包括：

- LightWare LW20/C
- TF-Luna
- Garmin Lite V3
- VL53L1CB

### RaccoonLab Cyphal和DroneCAN µRANGEFINDER

[RaccoonLab µRANGEFINDER](https://docs.raccoonlab.co/guide/rangefinder/uRANGEFINDER.html)设计用于测量距离并通过Cyphal/DroneCAN协议发布数据。
可用于估算精确着陆或障碍物规避。

特性：

- [VL53L1CBV0FY-1](https://www.st.com/resource/en/datasheet/vl53l1.pdf) 传感器
- 输入电压传感器
- CAN接口：2个[CAN接口](https://en.wikipedia.org/wiki/CAN_bus)

## 配置/设置 {#configuration}

测距仪通常连接到串口（PWM）或I2C端口（具体取决于设备驱动），通过设置特定参数在端口上启用。

针对每种距离传感器的硬件和软件配置在各自的主题中单独说明。

所有距离传感器通用的配置（涵盖物理设置和使用方法）如下：

### 通用配置

通用测距仪配置通过 [EKF2_RNG\_\*](../advanced_config/parameter_reference.md#EKF2_RNG_CTRL) 参数指定。  
这些参数包括（不限于）：

- [EKF2_RNG_POS_X](../advanced_config/parameter_reference.md#EKF2_RNG_POS_X), [EKF2_RNG_POS_Y](../advanced_config/parameter_reference.md#EKF2_RNG_POS_Y), [EKF2_RNG_POS_Z](../advanced_config/parameter_reference.md#EKF2_RNG_POS_Z) - 测距仪相对于机体重心在X、Y、Z方向的偏移量。
- [EKF2_RNG_PITCH](../advanced_config/parameter_reference.md#EKF2_RNG_PITCH) - 0度（默认值）表示测距仪与机体垂直轴完全对齐（即垂直向下），90度表示测距仪朝前指向。  
  使用简单三角函数计算非零俯仰角时的地面距离。
- [EKF2_RNG_DELAY](../advanced_config/parameter_reference.md#EKF2_RNG_DELAY) - 数据从传感器到达估计器的近似延迟。
- [EKF2_RNG_SFE](../advanced_config/parameter_reference.md#EKF2_RNG_SFE) - 测距仪距离相关的噪声缩放因子。
- [EKF2_RNG_NOISE](../advanced_config/parameter_reference.md#EKF2_RNG_NOISE) - 测距仪融合的测量噪声## 测试

测试测距仪的最简单方法是改变距离并与PX4检测到的值进行比较。  
以下章节展示了获取测量距离的几种方法。

### QGroundControl MAVLink Inspector

_QGroundControl MAVLink Inspector_ 可用于查看从机体发送的消息，包括测距仪的 `DISTANCE_SENSOR` 信息。  
工具的主要区别在于 _Analyze_ 工具可以将值绘制成图表。

::: info
发送的消息取决于机体配置。  
只有连接的机体安装了测距仪并正在发布传感器值时，才会收到 `DISTANCE_SENSOR` 消息。
:::

查看测距仪输出的步骤：

1. 打开菜单 **Q > 选择工具 > 分析工具**：

   ![QGC分析工具菜单](../../assets/qgc/analyze/menu_analyze_tool.png)

1. 选择消息 `DISTANCE_SENSOR`，然后勾选 `current_distance` 的绘图选项。  
   工具将绘制结果：  
   ![QGC分析 DISTANCE_SENSOR 值](../../assets/qgc/analyze/qgc_analyze_tool_distance_sensor.png)

### QGroundControl MAVLink 控制台

你也可以使用 _QGroundControl MAVLink 控制台_ 查看 `distance_sensor` uORB 主题：

```sh
listener distance_sensor 5
```

::: info
_QGroundControl MAVLink 控制台_ 在连接到 Pixhawk 或其他 NuttX 目标时可用，但不适用于模拟器。  
在模拟器上，可以直接在终端运行命令。
:::

更多信息请参见：[开发 > 调试/日志 > 使用监听器命令调试传感器/主题](../debug/sensor_uorb_topic_debugging.md)

## 模拟

### Gazebo 模拟

激光雷达和声纳测距仪可用于 [Gazebo](../sim_gazebo_gz/index.md) 模拟器。  
为此必须使用包含测距仪的机体模型启动模拟器。

向下朝向的传感器写入 [DistanceSensor](../msg_docs/DistanceSensor.md) UORB 主题，可用于测试 [降落](../flight_modes_mc/land.md) 和 [地形跟随](../flying/terrain_following_holding.md) 等用例：

- [四旋翼(x500) 配备1D激光雷达(向下朝向)](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-1d-lidar-down-facing)

  ```sh
  make px4_sitl gz_x500_lidar_down
  ```

前向朝向的传感器写入 [ObstacleDistance](../msg_docs/ObstacleDistance.md) 可用于测试 [防撞](../computer_vision/collision_prevention.md#gazebo-simulation)：

- [四旋翼(x500) 配备2D激光雷达](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-2d-lidar)

  ```sh
  make px4_sitl gz_x500_lidar_2d
  ```

- [四旋翼(x500) 配备1D激光雷达(前向朝向)](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-1d-lidar-front-facing)

  ```sh
  make px4_sitl gz_x500_lidar_front
  ```

### Gazebo-Classic 模拟

激光雷达和声纳测距仪可用于 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟器。  
为此必须使用包含测距仪的机体模型启动模拟器。

iris 光流模型包含激光雷达测距仪：

```sh
make px4_sitl gazebo-classic_iris_opt_flow
```

typhoon_h480 包含声纳测距仪：

```sh
make px4_sitl gazebo-classic_typhoon_h480
```

如果需要使用其他机体，可以在其配置文件中包含模型。  
可以参考 iris 和 Typhoon 的配置文件：

- [iris_opt_flow.sdf](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/models/iris_opt_flow/iris_opt_flow.sdf)

  ```xml
    <include>
      <uri>model://lidar</uri>
      <pose>-0.12 0 0 0 3.1415 0</pose>
    </include>
    <joint name="lidar_joint" type="revolute">
      <child>lidar::link</child>
      <parent>iris::base_link</parent>
      <axis>
        <xyz>0 0 1</xyz>
        <limit>
          <upper>0</upper>
          <lower>0</lower>
        </limit>
      </axis>
    </joint>
  ```

- [typhoon_h480.sdf](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/models/typhoon_h480/typhoon_h480.sdf.jinja#L1131-L1145)

  ```xml
    <include>
      <uri>model://sonar</uri>
    </include>
    <joint name="sonar_joint" type="revolute">
      <child>sonar_model::link</child>
      <parent>typhoon_h480::base_link</parent>
      <axis>
        <xyz>0 0 1</xyz>
        <limit>
          <upper>0</upper>
          <lower>0</lower>
        </limit>
      </axis>
    </joint>
  ```