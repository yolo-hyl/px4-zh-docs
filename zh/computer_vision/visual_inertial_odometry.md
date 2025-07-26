# 视觉惯性里程计 (VIO)

_Visual Inertial Odometry_（视觉惯性里程计）是一种[计算机视觉](../computer_vision/index.md)技术，用于估计移动机体相对于某一_局部_起始位置的3D_姿态_（局部位置和方向）和_速度_。
它常用于在GPS缺失或不可靠的场景中导航机体（例如室内环境或桥梁下方飞行时）。

VIO通过[视觉里程计](https://en.wikipedia.org/wiki/Visual_odometry)从相机图像中估计机体_姿态_，并结合机体IMU的惯性测量数据（以修正由于机体快速移动导致图像采集质量差而产生的误差）。

本主题提供配置PX4和协同计算机以实现VIO设置的指导。

::: info
建议的设置使用ROS进行VIO信息到PX4的路由。
不过，只要消息通过适当的[MAVLink接口](../ros/external_position_estimation.md#px4-mavlink-integration)提供，PX4本身并不关心消息来源。
:::

## 建议配置

以下章节建议了VIO（视觉惯性里程计）的软硬件配置方案，用于说明如何将VIO系统与PX4对接。该方案使用现成的跟踪相机和运行ROS的协处理器，通过ROS读取相机的里程计信息并发送给PX4。

一个合适的跟踪相机示例是[Intel® RealSense™ Tracking Camera T265](../peripherals/camera_t265_vio.md)。

### 相机安装

将相机连接到协处理器并安装到机体框架上：

- 如果可能，请将相机镜头朝下安装（默认方式）。
- 相机通常对振动非常敏感，建议采用软性安装（如使用减震泡沫）。

### 协处理器配置

要配置ROS和PX4：

- 在协处理器上安装并配置[MAVROS](../ros/mavros_installation.md)。
- 实现并运行一个ROS节点，从相机读取数据并通过MAVROS发布VIO里程计信息。
  - 详见下方的[VIO ROS节点](#vio_ros_node)部分了解该节点的具体要求。
- 按照[下方](#ekf2_tuning)的说明调整PX4的EKF2估计器参数。
- 验证与飞控的连接。

  :::tip
  可使用_QGroundControl_的[MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html)验证是否接收到`ODOMETRY`或`VISION_POSITION_ESTIMATE`消息（或检查`HEARTBEAT`消息的组件ID是否为197（`MAV_COMP_ID_VISUAL_INERTIAL_ODOMETRY`）。
  :::

- [在首次飞行前验证VIO配置](#verify_estimate)！

<a id="vio_ros_node"></a>

### ROS VIO节点

在建议的配置中，需要ROS节点完成以下功能：

1. 与选定的相机或传感器硬件进行交互，
2. 生成包含位置估计的里程计消息，并通过MAVROS发送给PX4，
3. 发布消息以指示VIO系统状态。

ROS节点的具体实现取决于使用的相机类型，需要开发适配相机接口和驱动的代码。

里程计消息应为类型[`nav_msgs/Odometry`](http://docs.ros.org/en/noetic/api/nav_msgs/html/msg/Odometry.html)，并发布到`/mavros/odometry/out`主题。

系统状态消息应为类型[`mavros_msgs/CompanionProcessStatus`](https://github.com/mavlink/mavros/blob/master/mavros_msgs/msg/CompanionProcessStatus.msg)，并发布到`/mavros/companion_process/status`主题。这些消息应标识组件为`MAV_COMP_ID_VISUAL_INERTIAL_ODOMETRY`（197），并指示系统的`state`。推荐的状态值包括：

- 当VIO系统正常运行时使用`MAV_STATE_ACTIVE`，
- 当VIO系统运行但置信度较低时使用`MAV_STATE_CRITICAL`，
- 当系统故障或估计置信度不可接受时使用`MAV_STATE_FLIGHT_TERMINATION`。

<a id="ekf2_tuning"></a>

### PX4调参

使用EKF2结合外部位置信息时，必须设置以下参数。

| 参数                                                                                                                                                                                                 | 外部位置估计设置                                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| [EKF2_EV_CTRL](../advanced_config/parameter_reference.md#EKF2_EV_CTRL)                                                                                                                              | 根据所需融合模型设置_水平位置融合_、_垂直视觉融合_、_速度融合_和_偏航角融合_。                                                             |
| [EKF2_HGT_REF](../advanced_config/parameter_reference.md#EKF2_HGT_REF)                                                                                                                              | 设置为_Vision_，将视觉传感器作为高度估计的参考传感器。                                                                                   |
| [EKF2_EV_DELAY](../advanced_config/parameter_reference.md#EKF2_EV_DELAY)                                                                                                                            | 设置为测量时间戳与"实际"捕获时间的差值。更多细节请参阅[下方](#tuning-EKF2_EV_DELAY)。                                                   |
| [EKF2_EV_POS_X](../advanced_config/parameter_reference.md#EKF2_EV_POS_X), [EKF2_EV_POS_Y](../advanced_config/parameter_reference.md#EKF2_EV_POS_Y), [EKF2_EV_POS_Z](../advanced_config/parameter_reference.md#EKF2_EV_POS_Z) | 设置视觉传感器相对于机体坐标系的位置。                                                                                                   |

这些参数可在_QGroundControl_ > **Vehicle Setup > Parameters > EKF2** 中设置（更改参数后需重启飞控以生效）。

更多详细信息请参阅：[使用PX4的导航滤波器（EKF2）> 外部视觉系统](../advanced_config/tuning_the_ecl_ekf.md#external-vision-system)。

<a id="tuning-EKF2_EV_DELAY"></a>

#### 调整EKF2_EV_DELAY

[EKF2_EV_DELAY](../advanced_config/parameter_reference.md#EKF2_EV_DELAY)是_视觉位置估计器相对于IMU测量的延迟_。换句话说，它是视觉系统时间戳与IMU时钟记录的"实际"捕获时间之间的差值（EKF2的"基准时钟"）。

如果存在正确的时间戳（不仅是到达时间）和时间同步（例如NTP）在MoCap和（例如）ROS计算机之间，理论上可以设置为0。实际上，可能需要根据通信链路的延迟进行经验调整，因为延迟与具体配置密切相关。完全同步的系统链路非常罕见！

可以通过检查IMU速率和EV速率之间的偏移量，从日志中粗略估算延迟：

![ekf2_ev_delay日志](../../assets/ekf2/ekf2_ev_delay_tuning.png)

::: info
外部数据与机载估计的对比图（如上图）可以使用[FlightPlot](../log/flight_log_analysis.md#flightplot)或类似的飞行分析工具生成。
:::

通过调整参数值，找到在动态操作期间产生最低EKF创新值的数值，可以进一步优化该参数。

<a id="verify_estimate"></a>

## 检查/验证VIO估计

::: info
下面提到的 [MAV_ODOM_LP](../advanced_config/parameter_reference.md#MAV_ODOM_LP) 参数在 PX4 v1.14 中已被移除。
本节内容需要更新。 <!-- https://github.com/PX4/PX4-Autopilot/pull/20501#issuecomment-1993788815 -->
:::

在首次飞行前，请执行以下检查以验证 VIO 是否正常工作：

- 将 PX4 参数 `MAV_ODOM_LP` 设置为 `1`。  
  PX4 将通过 MAVLink [ODOMETRY](https://mavlink.io/en/messages/common.html#ODOMETRY) 消息回传接收到的外部位姿。  
  可以通过 _QGroundControl_ [MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 检查这些 MAVLink 消息。
- 旋转机体，直到 `ODOMETRY` 消息的四元数接近单位四元数（w=1, x=y=z=0）。  
  - 此时，机体坐标系与外部位姿系统的参考坐标系对齐。  
  - 如果在不进行翻滚或俯仰的情况下无法获得接近单位四元数的四元数，说明机体坐标系仍存在俯仰或翻滚偏移。  
    如果出现这种情况，请勿继续操作并重新检查坐标系。
- 对齐后，将机体从地面抬起，应看到位置的 z 坐标减少。  
  向前移动机体时，位置的 x 坐标应增加。  
  向右移动机体时，位置的 y 坐标应增加。
- 确认消息中的线速度是在 _FRD_ 机体坐标系参考系中表示的。
- 将 PX4 参数 `MAV_ODOM_LP` 恢复为 0。  
  PX4 将停止回传 `ODOMETRY` 消息。

如果上述步骤一致，可以尝试首次飞行：

1. 将机体放置在地面并开始回传 `ODOMETRY` 反馈（如上）。  
   降低油门杆并解锁电机。  

   此时，左杆处于最低位置时切换到位置控制模式。  
   指示灯应为绿色。  
   绿色指示灯表示位置反馈可用且位置控制已激活。

1. 将油门杆置于中间（死区）以维持高度。  
   提高杆位将增加参考高度，降低杆位将减少高度。  
   同样，其他杆位将改变相对于地面的位置。
1. 提高油门杆使机体起飞，随后立即将其移回中间位置。
1. 确认机体能够保持位置。

## 故障排除

首先确保 MAVROS 能够成功连接到飞控。

如果连接正常但出现以下常见问题/解决方案：

- **问题**：无人机飞行时出现漂移/失控飞离，但手动携带时不出现此问题（假设螺旋桨已取下）。

  - 如果使用 [T265](../peripherals/camera_t265_vio.md) 相机，尝试采用软性安装（该相机对高频振动非常敏感）。

- **问题**：启用 VIO 时出现 toilet-bowling 现象。

  - 确保相机方向与启动文件中的坐标变换一致。
    使用 _QGroundControl_ [MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 验证来自 MAVROS 的 `ODOMETRY` 消息中的速度是否与 FRD 坐标系对齐。

- **问题**：希望使用视觉定位进行回环闭合，同时运行 GPS。
  - 这会非常困难，因为两者不一致会混淆 EKF。
    根据测试，仅使用视觉速度更为可靠（如果你找到可靠的配置方式，请告知我们）。

## 开发者信息

有兴趣扩展该实现（或编写一个不依赖ROS的不同实现）的开发者请参阅[使用视觉系统或运动捕捉系统进行位置估计](../ros/external_position_estimation.md)。

本主题还解释了如何将VIO配置为与LPE Estimator一起使用（已弃用）。

## 更多信息

- [使用 PX4 的导航滤波器 (EKF2) > 外部视觉系统](../advanced_config/tuning_the_ecl_ekf.md#external-vision-system)