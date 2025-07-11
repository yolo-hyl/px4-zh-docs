# Gazebo 机体

本主题列出/展示 PX4 [Gazebo](../sim_gazebo_gz/index.md) 仿真支持的机体，以及运行它们所需的 `make` 命令（这些命令需在 **PX4-Autopilot** 目录的终端中执行）。

模型以子模块形式包含在 PX4 中，该子模块从 [Gazebo Models Repository](../sim_gazebo_gz/gazebo_models.md) 获取。

支持的机体类型包括：多旋翼、VTOL、固定翼飞机、地面车。

:::warning
请参见 [Gazebo Classic Vehicles](../sim_gazebo_classic/vehicles.md) 以了解适用于旧版 [Gazebo "Classic" 仿真](../sim_gazebo_classic/index.md) 的机体。
请注意，两个版本的模拟器之间机体模型不可互换：本页面的机体仅适用于 (new) [Gazebo](../sim_gazebo_gz/index.md)。
:::

## 多旋翼

### X500 四旋翼

```sh
make px4_sitl gz_x500
```

### X500四旋翼无人机与视觉里程计

```sh
make px4_sitl gz_x500_vision
```

![Gazebo中的x500](../../assets/simulation/gazebo/vehicles/x500.png)

### X500四旋翼飞行器搭载深度相机（前视）

该模型配备了一个向前的深度相机，基于[OAK-D](https://shop.luxonis.com/products/oak-d)建模。

```sh
make px4_sitl gz_x500_depth
```

![x500 with depth camera in Gazebo](../../assets/simulation/gazebo/vehicles/x500_depth.png)

### X500四旋翼飞行器与单目摄像头

该模型附带一个简单的单目摄像头传感器（模型本身没有物理摄像头的可视化效果）。

```sh
make px4_sitl gz_x500_mono_cam
```

::: info
摄像头目前还不能用于在QGroundControl中传输视频或进行图像捕捉。
[PX4-Autopilot#22563](https://github.com/PX4/PX4-Autopilot/issues/22563) 可用于跟踪为完全启用这些用例所需进行的额外工作。
:::

### X500 四旋翼飞行器配单目相机（向下）

该模型配备了一个简单的向下单目相机传感器（模型本身没有物理相机的可视化效果）。

这可以与[Aruco世界](../sim_gazebo_gz/worlds.md#aruco)结合使用，以测试精确着陆。

```sh
make px4_sitl gz_x500_mono_cam_down
```

### X500四旋翼带1D LIDAR（下视）

该模型在底部安装了一个激光雷达，基于[Lightware LW20/C](../sensor/sfxx_lidar.md)设计。

其测距范围在0.1至100米之间。

该模型可用于测试[rangefinder](../sensor/rangefinders.md)应用场景，如[降落](../flight_modes_mc/land.md)或[地形跟随](../flying/terrain_following_holding.md)。

```sh
make px4_sitl gz_x500_lidar_down
```

![Gazebo中的X500下视1D LIDAR](../../assets/simulation/gazebo/vehicles/x500_lidar_down.png)

### X500四旋翼飞行器搭载1D激光雷达（前向）

该模型在前端安装了一个激光雷达，基于[Lightware LW20/C](../sensor/sfxx_lidar.md)进行建模。

其探测范围为0.2至100米。

该模型可用于测试[防撞](../computer_vision/collision_prevention.md#gazebo-simulation)。

```sh
make px4_sitl gz_x500_lidar_front
```

![X500搭载前向1D激光雷达的Gazebo仿真](../../assets/simulation/gazebo/vehicles/x500_lidar_front.png)

### X500四旋翼飞行器与2D激光雷达

该模型配备了一个二维激光雷达，基于[Hokuyo UTM-30LX](https://www.hokuyo-aut.jp/search/single.php?serial=169)建模。
其探测范围在0.1到30米之间，扫描角度为270度。
该模型可用于测试[防撞](../computer_vision/collision_prevention.md#gazebo-simulation)功能。

```sh
make px4_sitl gz_x500_lidar_2d
```

![Gazebo中的X500二维激光雷达四旋翼飞行器](../../assets/simulation/gazebo/vehicles/x500_lidar_2d.png)

::: info
传感器信息被写入由防撞功能使用的[ObstacleDistance](../msg_docs/ObstacleDistance.md) UORB消息中。
:::

### X500四旋翼飞行器带云台（前向）

此模型在前方安装了[云台](../advanced/gimbal_control.md)，角度范围为：

- 横滚: [- $\frac{\pi}{4}$, $\frac{\pi}{4}$]
- 俯仰: [- $\frac{3\pi}{4}$, $\frac{\pi}{4}$]
- 偏航: 无限旋转

云台关节使用带ZXY运动链的位姿控制。

![Gazebo中的四旋翼飞行器（X500）带云台（前向）](../../assets/simulation/gazebo/vehicles/x500_gimbal.png)

```sh
make px4_sitl gz_x500_gimbal
```

## 固定翼飞行器

### 标准飞机

```sh
make px4_sitl gz_rc_cessna
```

![Gazebo中的飞机](../../assets/simulation/gazebo/vehicles/rc_cessna.png)

### 高级飞机

<Badge type="tip" text="PX4 v1.15" />

```sh
make px4_sitl gz_advanced_plane
```

![Gazebo中的高级飞机](../../assets/simulation/gazebo/vehicles/advanced_plane.png)

::: info
高级飞机与"常规飞机"的区别在于两者使用的升力物理模型不同：

- 可以通过[Advanced Lift Drag Tool](../sim_gazebo_gz/tools_avl_automation.md)配置模型使用的_Advanced Lift Drag_插件，使其更贴近特定机体的特性。
- 关于高级飞机的升力计算细节，请参见[PX4-SITL_gazebo-classic/src/liftdrag_plugin/README.md](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/src/liftdrag_plugin/README.md)

:::

## 垂直起降

### 标准 VTOL

```sh
make px4_sitl gz_standard_vtol
```

![Standard VTOL in Gazebo Classic](../../assets/simulation/gazebo/vehicles/standard_vtol.png)

### 四旋尾坐式VTOL

一种使用差分推力进行俯仰、滚转和偏航控制的垂直起降尾坐式模型。

```sh
make px4_sitl gz_quadtailsitter
```

![VTOL quad tailsitter in Gazebo](../../assets/simulation/gazebo/vehicles/vtol_quad_tailsitter.png)

### 倾转旋翼 VTOL

一种 VTOL 飞机，其在转换阶段前两个电机将向前倾转以提供向前推力。

```sh
make px4_sitl gz_tiltrotor
```

![VTOL Tiltrotor in Gazebo](../../assets/simulation/gazebo/vehicles/vtol_tiltrotor.png)

## 漫游车

### 差速车

[差速车](../frames_rover/index.md#differential) 默认使用 [车世界](../sim_gazebo_gz/worlds.md#rover)。

```sh
make px4_sitl gz_r1_rover
```

![差速车在Gazebo中的示意图](../../assets/simulation/gazebo/vehicles/rover_differential.png)

### Ackermann越野车

[Ackermann越野车](../frames_rover/index.md#ackermann) 默认使用[越野车世界](../sim_gazebo_gz/worlds.md#rover)。

```sh
make px4_sitl gz_rover_ackermann
```

![Gazebo中的Ackermann越野车](../../assets/simulation/gazebo/vehicles/rover_ackermann.png)