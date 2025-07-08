# Gazebo 机体

本主题列出了 PX4 [Gazebo](../sim_gazebo_gz/index.md) 仿真支持的机体，并提供了运行所需的 `make` 命令（命令需在 **PX4-Autopilot** 目录下的终端中执行）。

模型通过子模块包含在 PX4 中，该子模块从 [Gazebo Models Repository](../sim_gazebo_gz/gazebo_models.md) 获取。

支持的机体类型包括：多旋翼、垂直起降飞行器（VTOL）、固定翼飞机和地面车辆（Rover）。

:::warning
使用旧版 [Gazebo "Classic" 仿真](../sim_gazebo_classic/index.md) 的机体请参考 [Gazebo Classic 机体](../sim_gazebo_classic/vehicles.md)。
注意：两个仿真器版本的机体模型不可互换，此页面的机体仅适用于新版 [Gazebo](../sim_gazebo_gz/index.md)。
:::

## 多旋翼

### X500 四旋翼飞行器

```sh
make px4_sitl gz_x500
```

### 带视觉里程计的 X500 四旋翼飞行器

```sh
make px4_sitl gz_x500_vision
```

![Gazebo中的x500四旋翼飞行器](../../assets/simulation/gazebo/vehicles/x500.png)

### 带深度相机（前向）的 X500 四旋翼飞行器

该模型配备了前向深度相机，基于 [OAK-D](https://shop.luxonis.com/products/oak-d) 设计。

```sh
make px4_sitl gz_x500_depth
```

![Gazebo中带深度相机的x500四旋翼飞行器](../../assets/simulation/gazebo/vehicles/x500_depth.png)

### 带单目相机的 X500 四旋翼飞行器

该模型配备了简单单目相机传感器（模型本身无物理相机可视化）。

```sh
make px4_sitl gz_x500_mono_cam
```

::: info
该相机目前尚无法在 QGroundControl 中用于视频流传输或图像捕获。
[PX4-Autopilot#22563](https://github.com/PX4/PX4-Autopilot/issues/22563) 可用于跟踪实现这些功能所需的额外工作。
:::

### 带下向单目相机的 X500 四旋翼飞行器

该模型配备了向下视角的单目相机传感器（模型本身无物理相机可视化）。

可用于 [Aruco 世界](../sim_gazebo_gz/worlds.md#aruco) 进行精确着陆测试。

```sh
make px4_sitl gz_x500_mono_cam_down
```

### 带1D激光雷达（下向）的 X500 四旋翼飞行器

该模型底部配备了1D激光雷达，基于 [Lightware LW20/C](../sensor/sfxx_lidar.md) 设计。

探测范围在0.1至100米之间。

可用于测试 [测距仪](../sensor/rangefinders.md) 应用场景，如 [着陆](../flight_modes_mc/land.md) 或 [地形跟随](../flying/terrain_following_holding.md)。

```sh
make px4_sitl gz_x500_lidar_down
```

![Gazebo中带下向1D激光雷达的x500四旋翼飞行器](../../assets/simulation/gazebo/vehicles/x500_lidar_down.png)

### 带1D激光雷达（前向）的 X500 四旋翼飞行器

该模型前部配备了1D激光雷达，基于 [Lightware LW20/C](../sensor/sfxx_lidar.md) 设计。

探测范围在0.2至100米之间。

可用于测试 [防撞](../computer_vision/collision_prevention.md#gazebo-simulation)。

```sh
make px4_sitl gz_x500_lidar_front
```

![Gazebo中带前向1D激光雷达的x500四旋翼飞行器](../../assets/simulation/gazebo/vehicles/x500_lidar_front.png)

### 带2D激光雷达的 X500 四旋翼飞行器

该模型配备了2D激光雷达，用于环境扫描和避障。

```sh
make px4_sitl gz_x500_lidar_2d
```

::: info
该模型适用于SLAM（同步定位与地图构建）算法测试。
:::

## 垂直起降飞行器 (VTOL)

### 带差分GPS的 VTOL 机体

该模型集成了差分GPS模块，适用于高精度定位任务。

```sh
make px4_sitl gz_vtol_dgps
```

::: warning
运行该模型需要启用RTK基站支持。
:::

## 固定翼飞机

### 带襟翼系统的固定翼飞机

该模型支持襟翼展开/收起操作，用于升力调节和着陆速度控制。

```sh
make px4_sitl gz_fixed_wing_flaps
```

![带襟翼系统的固定翼飞机](../../assets/simulation/gazebo/vehicles/fixed_wing_flaps.png)

## 地面车辆 (Rover)

### 差速转向地面车辆

该模型使用差速转向机制，适合简单地形导航。

```sh
make px4_sitl gz_rover_diff
```

![差速转向地面车辆](../../assets/simulation/gazebo/vehicles/rover_diff.png)

### 独立悬挂式地面车辆

该模型配备了独立悬挂系统，适用于复杂地形越野。

```sh
make px4_sitl gz_rover_4wd
```

![独立悬挂式地面车辆](../../assets/simulation/gazebo/vehicles/rover_4wd.png)

## 传感器扩展模型

### 多传感器融合机体

该模型集成了以下传感器：
- RGB-D 相机
- IMU
- GPS
- 超声波传感器

```sh
make px4_sitl gz_sensor_fusion
```

::: info
该模型可用于多传感器数据融合算法开发。
:::

## 特殊用途机体

### 消防无人机

该模型配备了水箱和喷洒系统，用于模拟消防任务。

```sh
make px4_sitl gz_fire_extinguishing
```

![消防无人机](../../assets/simulation/gazebo/vehicles/fire_extinguishing.png)

### 医疗物资运输无人机

该模型设计有专用物资舱，支持温度控制功能。

```sh
make px4_sitl gz_medical_delivery
```

![医疗物资运输无人机](../../assets/simulation/gazebo/vehicles/medical_delivery.png)

## 开发者工具

### 虚拟调试机体

该模型专为调试设计，支持以下特性：
- 可配置故障注入
- 模拟信号干扰
- 实时性能监控

```sh
make px4_sitl gz_debug
```

::: warning
该模型仅适用于开发测试，不支持实际飞行任务。
:::