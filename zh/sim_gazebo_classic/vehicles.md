# Gazebo Classic 机体

本主题列出 PX4 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟器支持的机体及运行它们所需的 `make` 命令（命令需在 **PX4-Autopilot** 目录的终端中运行）。

支持的机体类型包括：多旋翼、垂直起降飞行器（VTOL）、垂直起降尾座式飞行器（VTOL Tailsitter）、固定翼飞机、地面无人车（Rover）、水下无人潜航器/UUV。

::: info
[Gazebo Classic](../sim_gazebo_classic/index.md) 页面介绍了如何安装 Gazebo Classic、如何启用视频和加载自定义地图，以及许多其他配置选项。
:::

## 多旋翼

### 四旋翼（默认）

```sh
make px4_sitl gazebo-classic
```

### 带光流传感器的四旋翼

```sh
make px4_sitl gazebo-classic_iris_opt_flow
```

### 带深度相机的四旋翼

这些模型装配了深度相机，基于 Intel® RealSense™ D455 建模。

_前向深度相机：_

```sh
make px4_sitl gazebo-classic_iris_depth_camera
```

_下向深度相机：_

```sh
make px4_sitl gazebo-classic_iris_downward_depth_camera
```

### 3DR Solo（四旋翼）

```sh
make px4_sitl gazebo-classic_solo
```

![3DR Solo 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/solo.png)

### Typhoon H480（六旋翼）

```sh
make px4_sitl gazebo-classic_typhoon_h480
```

![Typhoon H480 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/typhoon.jpg)

::: info
此目标还支持 [视频流模拟](../sim_gazebo_classic/index.md#video-streaming)。
:::

<a id="fixed_wing"></a>

## 飞机/固定翼

### 标准飞机

```sh
make px4_sitl gazebo-classic_plane
```

![飞机在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/plane.png)

#### 带弹射发射的飞机

```sh
make px4_sitl gazebo-classic_plane_catapult
```

该模型模拟手动/弹射发射，可用于 [固定翼起飞](../flight_modes_fw/takeoff.md) 的位置模式、起飞模式或任务规划。

在机体解除武装后，飞机将自动发射。

## 垂直起降飞行器（VTOL）

### 标准 VTOL

```sh
make px4_sitl gazebo-classic_standard_vtol
```

![标准 VTOL 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/standard_vtol.png)

### 尾座式 VTOL

```sh
make px4_sitl gazebo-classic_tailsitter
```

![尾座式 VTOL 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/tailsitter.png)

<a id="ugv"></a>

## 无人地面车辆（UGV/Rover/Car）

### Ackermann 结构 UGV

```sh
make px4_sitl gazebo-classic_rover
```

![Rover 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/rover.png)

### 差速结构 UGV

```sh
make px4_sitl gazebo-classic_r1_rover
```

![Rover 在 Gazebo Classic](../../assets/simulation/gazebo_classic/vehicles/r1_rover.png)

## 无人水下航行器（UUV/潜艇）

### HippoCampus TUHH UUV

```sh
make px4_sitl gazebo-classic_uuv_hippocampus
```

![潜艇/UUV](../../assets/simulation/gazebo_classic/vehicles/hippocampus.png)

## 无人水面舰艇（USV/船）

<a id="usv_boat"></a>

### 船（USV）

```sh
make px4_sitl gazebo-classic_boat
```

![船/USV](../../assets/simulation/gazebo_classic/vehicles/boat.png)

<a id="airship"></a>

## 空气动力飞船

### Cloudship

```sh
make px4_sitl gazebo-classic_cloudship
```

![空气动力飞船](../../assets/simulation/gazebo_classic/vehicles/airship.png)