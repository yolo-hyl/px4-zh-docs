# Gazebo 世界

本节提供关于PX4支持的[Gazebo](../sim_gazebo_gz/index.md)世界的图像/信息。

[默认世界](#default)会默认加载，但可以通过[特定模型世界](#model_specific_worlds)覆盖。
开发者也可以手动指定加载的世界：[Gazebo > 指定世界](../sim_gazebo_gz/index.md#specify-world)（或[Gazebo模型仓库](../sim_gazebo_gz/gazebo_models.md#gazebo-models-repository-px4-gazebo-models)）。

支持世界源代码可在GitHub的[Gazebo Models Repository](../sim_gazebo_gz/gazebo_models.md#gazebo-models-repository-px4-gazebo-models)中找到：[PX4/PX4-gazebo-models/tree/main/worlds](https://github.com/PX4/PX4-gazebo-models/tree/main/worlds)。

## 空世界（默认）{#default}

空世界（灰色平面）
默认使用该世界。

[PX4-gazebo-models/main/worlds/default.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/default.sdf)

![默认世界截图](../../assets/simulation/gazebo/worlds/default.png)

## Aruco

Aruco世界是默认世界中添加了[ArUco标记](https://docs.opencv.org/4.x/d5/dae/tutorial_aruco_detection.html)的版本。

与[x500_mono_cam_down](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-monocular-camera-down-facing)机体配合使用，用于测试[精准降落](../advanced_features/precland.md)。

[PX4-gazebo-models/main/worlds/aruco.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/aruco.sdf)

![Aruco世界截图](../../assets/simulation/gazebo/worlds/aruco.png)

## Baylands

被水域环绕的世界。

[PX4-gazebo-models/main/worlds/bayland.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/baylands.sdf)

![Baylands世界截图](../../assets/simulation/gazebo/worlds/baylands.png)

## Lawn

由绿色平面构成的世界，是[Rover世界](#Rover)的优化替代方案。
由于帧率较低，会导致部分框架出现分段错误，因此不推荐使用。

[PX4-gazebo-models/main/worlds/lawn.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/lawn.sdf)

![lawn世界截图](../../assets/simulation/gazebo/worlds/lawn.png)

## Rover

Rover世界专为地面车辆优化（将进一步优化），是[Ackermann Rover (4012)](../frames_rover/index.md#ackermann) (`make px4_sitl gz_rover_ackermann`)和[Differential Rover ((r1-rover (4009))](../frames_rover/index.md#differential) (`make px4_sitl gz_r1_rover`)的默认世界。

[PX4-gazebo-models/main/worlds/rover.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/rover.sdf)

![Rover世界截图](../../assets/simulation/gazebo/worlds/rover.png)

::: info
Rover世界与[Lawn世界](#Lawn)非常相似，但有以下两个主要区别：

- 地面网格，驾驶时作为参考。
- 更高的更新率，解决了Ackermann转向车辆的分段错误问题。

:::

## Walls

包含墙壁的测试世界，用于测试[碰撞预防](../computer_vision/collision_prevention.md)。

[PX4-gazebo-models/main/worlds/walls.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/walls.sdf)

![walls世界截图](../../assets/simulation/gazebo/worlds/walls.png)

## Windy

[空世界](#default)的带风版本。

[PX4-gazebo-models/main/worlds/windy.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/windy.sdf)

## Moving Platform

<Badge type="tip" text="PX4 v1.16" />

[空世界](#default)中添加了可移动平台，用于模拟从移动车辆（如船舶或卡车）上操作无人机。平台由包含在世界中的插件控制。平台高度为2米，使用以下命令将其放置在平台上：

```
PX4_GZ_MODEL_POSE=0,0,2.2 PX4_GZ_WORLD=moving_platform make px4_sitl gz_standard_vtol
```

插件可通过以下环境变量配置：

 - `PX4_GZ_PLATFORM_VEL`: 平台速度（m/s）。
 - `PX4_GZ_PLATFORM_HEADING_DEG`: 平台航向和速度方向（度）。0 = 东，正方向为逆时针。

[PX4-gazebo-models/main/worlds/moving_platform.sdf](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/moving_platform.sdf)

![moving platform世界截图](../../assets/simulation/gazebo/worlds/moving_platform.png)

::: tip
移动平台插件也可在其他世界中使用。
更多信息请参见插件[README](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/gz_plugins/moving_platform_controller/README.md)。
:::

## 特定模型世界{#model_specific_worlds}

某些[机体模型](../sim_gazebo_gz/vehicles.md)依赖于特定世界的物理/插件。
如果存在同名世界，PX4工具链会自动加载该世界（而非[默认世界](#default))：

特定模型世界包括：

- [Aruco世界](#Aruco): 带有[ArUco标记](https://docs.opencv.org/4.x/d5/dae/tutorial_aruco_detection.html)的默认世界，可与[x500_mono_cam_down](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-monocular-camera-down-facing)配合使用，用于测试[精准降落](../advanced_features/precland.md)。