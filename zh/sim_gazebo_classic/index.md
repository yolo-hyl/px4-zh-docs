# Gazebo Classic仿真

:::warning
[Gazebo](../sim_gazebo_gz/index.md) 在PX4上的功能即将与Gazebo Classic达到一致，将很快取代它。
在此之前，您仍可以在Ubuntu 22.04上继续使用Gazebo Classic处理仍需使用的极少数情况。
更多信息请参见 [PX4-Autopilot#23602: GZ Feature tracker](https://github.com/PX4/PX4-Autopilot/issues/23602)。
:::

Gazebo Classic 是一个强大的3D仿真环境，专为自主机器人设计，特别适合测试避障和计算机视觉。  
本页面描述其与SITL和单个机体的使用方式。  
Gazebo Classic也可与[HITL](../simulation/hitl.md)一起使用，或用于[多机体仿真](../sim_gazebo_classic/multi_vehicle_simulation.md)。

**支持的机体类型**：四旋翼（[Iris](../airframes/airframe_reference.md#copter_quadrotor_x_generic_quadcopter)）、六旋翼（Typhoon H480）、[通用标准垂直起降（QuadPlane）](../airframes/airframe_reference.md#vtol_standard_vtol_generic_standard_vtol)、尾座式、固定翼、Rover、潜艇/UUV。

<lite-youtube videoid="qfFF9-0k4KA" title="PX4 Flight Stack ROS 3D Software in the Loop Simulation (SITL)"/>

[![Mermaid Graph: Gazebo plugin](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIEdhemViby0tPlBsdWdpbjtcbiAgUGx1Z2luLS0-TUFWTGluaztcbiAgTUFWTGluay0tPlNJVEw7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggTFI7XG4gIEdhemViby0tPlBsdWdpbjtcbiAgUGx1Z2luLS0-TUFWTGluaztcbiAgTUFWTGluay0tPlNJVEw7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

<!-- original graph info
graph LR;
  Gazebo-- >Plugin;
  Plugin-- >MAVLink;
  MAVLink-- >SITL;
-->

::: info
有关仿真器、仿真环境和仿真配置（例如支持的机体）的通用信息，请参见[仿真](../simulation/index.md)。
:::

## 安装

::: info
如果你计划将 PX4 与 ROS 一起使用，**应遵循** [ROS 指南](../simulation/ros_interface.md) 来安装 ROS 和 Gazebo Classic（从而避免安装冲突）。
:::

标准安装脚本 ([/Tools/setup/ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh)) 会安装 [Gazebo](../sim_gazebo_gz/index.md) (Harmonic) 模拟器。
如果你希望在 _Ubuntu 22.04 (仅限)_ 上使用 Gazebo Classic，可以使用以下命令先移除 Gazebo 再重新安装 Gazebo-Classic 11：

```sh
sudo apt remove gz-harmonic
sudo apt install aptitude
sudo aptitude install gazebo libgazebo11 libgazebo-dev
```

请注意 `aptitude` 是必需的，因为它可以解决 `apt` 无法处理的依赖冲突（通过移除某些包）。

::: tip
你也可以在首次运行安装脚本之前，修改脚本以在 Ubuntu 22.04 上安装 Gazebo Classic。

其他安装说明可在 [gazebosim.org](http://gazebosim.org/tutorials?cat=guided_b&tut=guided_b1) 找到。
:::

## 运行模拟

通过启动 PX4 SITL 和 Gazebo Classic 并加载指定的机体配置（支持多旋翼、固定翼、垂直起降、光流传感器以及多机体模拟）来运行模拟。

最简单的方法是打开 PX4 _PX4-Autopilot_ 仓库根目录的终端并调用所需目标的 `make` 命令。  
例如，启动四旋翼模拟（默认配置）：

```sh
cd /path/to/PX4-Autopilot
make px4_sitl gazebo-classic
```

支持的机体和 `make` 命令如下（点击链接查看机体图片）。

::: info
要查看所有构建目标，请运行 `make px4_sitl list_vmd_make_targets`（并筛选以 `gazebo-classic_` 开头的目标）。
:::

| 机体                                                                                                                            | 命令                                                   |
| ---------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| [四旋翼（默认）](../sim_gazebo_classic/vehicles.md#quadrotor-default)                                                                   | `make px4_sitl gazebo-classic`                            |
| [带光流传感器的四旋翼](../sim_gazebo_classic/vehicles.md#quadrotor-with-optical-flow)                                       | `make px4_sitl gazebo-classic_iris_opt_flow`              |
| [带深度摄像头的四旋翼（前向）](../sim_gazebo_classic/vehicles.md#quadrotor-with-depth-camera)                      | `make px4_sitl gazebo-classic_iris_depth_camera`          |
| [带深度摄像头的四旋翼（下向）](../sim_gazebo_classic/vehicles.md#quadrotor-with-depth-camera)                     | `make px4_sitl gazebo-classic_iris_downward_depth_camera` |
| [3DR Solo（四旋翼）](../sim_gazebo_classic/vehicles.md#3dr-solo-quadrotor)                                                       | `make px4_sitl gazebo-classic_solo`                       |
| <a id="typhoon_h480"></a>[Typhoon H480（六旋翼）](../sim_gazebo_classic/vehicles.md#typhoon-h480-hexrotor) (带视频流) | `make px4_sitl gazebo-classic_typhoon_h480`               |
| [标准固定翼](../sim_gazebo_classic/vehicles.md#standard-plane)                                                                 | `make px4_sitl gazebo-classic_plane`                      |
| [带弹射发射的固定翼](../sim_gazebo_classic/vehicles.md#standard-plane-with-catapult-launch)                     | `make px4_sitl gazebo-classic_plane_catapult`             |
| [标准垂直起降](../sim_gazebo_classic/vehicles.md#standard-vtol)                                                                   | `make px4_sitl gazebo-classic_standard_vtol`              |
| [尾座垂直起降](../sim_gazebo_classic/vehicles.md#tailsitter-vtol)                                                               | `make px4_sitl gazebo-classic_tailsitter`                 |
| [差速底盘无人车](../sim_gazebo_classic/vehicles.md#ackermann-ugv)                                                            | `make px4_sitl gazebo-classic_rover`                      |
| [差速底盘无人车（R1型号）](../sim_gazebo_classic/vehicles.md#differential-ugv)                                                     | `make px4_sitl gazebo-classic_r1_rover`                   |
| [HippoCampus TUHH（水下无人潜航器）](../sim_gazebo_classic/vehicles.md#unmanned-underwater-vehicle-uuv-submarine) | `make px4_sitl gazebo-classic_uuv_hippocampus`            |
| [无人水面船（USV）](../sim_gazebo_classic/vehicles.md#hippocampus-tuhh-uuv)                                     | `make px4_sitl gazebo-classic_boat`                       |
| [飞艇（Cloudship）](../sim_gazebo_classic/vehicles.md#airship)                                                                   | `make px4_sitl gazebo-classic_cloudship`                  |

::: info
[安装文件和代码](../dev_setup/dev_env.md)指南是解决构建错误的有用参考资料。
:::

上述命令会启动一个完整用户界面的单机体模拟。其他选项包括：

- [分别启动 PX4 和 Gazebo](#分别启动 Gazebo 和 PX4)，以便保持 Gazebo Classic 运行并仅在需要时重新启动 PX4（比重启两者更快）。
- 以 [无头模式](#无头模式) 运行模拟，该模式不会启动 Gazebo Classic 用户界面（占用资源更少，运行速度更快）。

## 升空操作

上面的 `make` 命令首先构建 PX4，然后与 Gazebo Classic 模拟器一起运行。

PX4 启动后将显示如下的 PX4 Shell 界面：

```sh
______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.

INFO  [px4] Calling startup script: /bin/sh etc/init.d-posix/rcS 0
INFO  [param] selected parameter default file eeprom/parameters_10016
[param] Loaded: eeprom/parameters_10016
INFO  [dataman] Unknown restart, data manager file './dataman' size is 11798680 bytes
INFO  [simulator] Waiting for simulator to connect on TCP port 4560
Gazebo multi-robot simulator, version 9.0.0
Copyright (C) 2012 Open Source Robotics Foundation.
Released under the Apache 2 License.
http://gazebosim.org
...
INFO  [ecl/EKF] 5188000: commencing GPS fusion
```

控制台将打印 PX4 加载机体特定的初始化和参数文件的状态，并等待（并连接）到模拟器。  
一旦出现 `[ecl/EKF]` 开始 GPS 融合的信息提示，机体已准备好解锁。

::: info
右键点击四旋翼模型，可以通过上下文菜单启用跟随模式，方便保持视角跟随机体。
:::

![Gazebo Classic UI](../../assets/simulation/gazebo_classic/gazebo_follow.jpg)

可以通过以下命令使机体起飞：

```sh
pxh> commander takeoff
```

## 使用/配置选项

适用于所有模拟器的选项在顶层[仿真](../simulation/index.md#sitl-仿真环境)主题中进行了说明(其中部分可能在下方重复)。

### 模拟传感器/硬件故障

[模拟安全保护](../simulation/failsafes.md) 介绍了如何触发GPS故障和电池耗尽等安全保护机制。

### 无头模式

Gazebo Classic 可以以 _无头_ 模式运行，此时不会启动 Gazebo Classic UI。
这种模式启动更快且占用更少系统资源（即更"轻量级"的方式运行仿真）。

只需在常规的 `make` 命令前加上 `HEADLESS=1` 前缀，如下所示：

```sh
HEADLESS=1 make px4_sitl gazebo-classic_plane
```

<a id="custom_takeoff_location"></a>

### 设置自定义起飞位置

Gazebo Classic 中的起飞位置可以通过环境变量设置。
这将覆盖默认起飞位置以及[世界设置](#设置世界坐标)中的任何值。

需要设置的变量是：`PX4_HOME_LAT`、`PX4_HOME_LON` 和 `PX4_HOME_ALT`。

例如：

```sh
export PX4_HOME_LAT=28.452386
export PX4_HOME_LON=-13.867138
export PX4_HOME_ALT=28.5
make px4_sitl gazebo-classic
```

### 修改仿真速度

可通过环境变量 `PX4_SIM_SPEED_FACTOR` 提高或降低仿真速度相对于实时的速度。

以双倍实时速度运行：

```sh
PX4_SIM_SPEED_FACTOR=2 make px4_sitl_default gazebo-classic
```

以半倍实时速度运行：

```sh
PX4_SIM_SPEED_FACTOR=0.5  make px4_sitl_default gazebo-classic
```

若要对当前会话的所有 SITL 运行应用该因子，使用 `EXPORT`：

```sh
export PX4_SIM_SPEED_FACTOR=2
make px4_sitl_default gazebo-classic
```

### 修改风速

要模拟风速，请将此插件添加到您的世界文件中并设置 `windVelocityMean` 为 m/s（将 `SET_YOUR_WIND_SPEED` 替换为您想要的速度）。
如有需要，可调整 `windVelocityMax` 参数使其大于 `windVelocityMean`：

```xml
  <plugin name='wind_plugin' filename='libgazebo_wind_plugin.so'>
      <frameId>base_link</frameId>
      <robotNamespace/>
      <windVelocityMean>SET_YOUR_WIND_SPEED</windVelocityMean>
      <windVelocityMax>20.0</windVelocityMax>
      <windVelocityVariance>0</windVelocityVariance>
      <windDirectionMean>0 1 0</windDirectionMean>
      <windDirectionVariance>0</windDirectionVariance>
      <windGustStart>0</windGustStart>
      <windGustDuration>0</windGustDuration>
      <windGustVelocityMean>0</windGustVelocityMean>
      <windGustVelocityMax>20.0</windGustVelocityMax>
      <windGustVelocityVariance>0</windGustVelocityVariance>
      <windGustDirectionMean>1 0 0</windGustDirectionMean>
      <windGustDirectionVariance>0</windGustDirectionVariance>
      <windPubTopic>world_wind</windPubTopic>
    </plugin>
```

风向以方向向量（标准ENU约定）传递，将在gazebo插件中被归一化。
此外，您可以声明风速方差（m/s）²和基于正态分布的方向方差，以向仿真中添加一些随机因素。
阵风的处理方式与风相同，不同之处在于您可以通过以下两个参数 `windGustStart` 和 `windGustDuration` 指定开始时间和持续时间。

您可以在 [PX4/PX4-SITL_gazebo-classic/worlds/windy.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/windy.world#L15-L31) 中查看具体实现。

### 使用操纵杆

通过 _QGroundControl_ 支持操纵杆和拇指操纵杆（[设置说明在此](../simulation/index.md#joystick-gamepad-integration)）。

### 提高距离传感器性能

当前默认世界是 [PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds/**iris.world**](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/worlds))，它使用了高程图作为地面。

这可能会导致使用距离传感器时出现困难。
如果出现意外结果，建议将 **iris.model** 中的模型从 `uneven_ground` 改为 `asphalt_plane`。

### 模拟GPS噪声

Gazebo Classic 可以模拟类似真实系统中常见的GPS噪声（否则报告的GPS值将是无噪声/完美的）。
这对于开发可能受GPS噪声影响的应用程序（例如精确定位）非常有用。

如果目标机体的SDF文件包含 `gpsNoise` 元素的值（即包含行：`<gpsNoise>true</gpsNoise>`），则启用GPS噪声。
在许多机体SDF文件中默认启用：**solo.sdf**、**iris.sdf**、**standard_vtol.sdf**、**delta_wing.sdf**、**plane.sdf**、**typhoon_h480**、**tailsitter.sdf**。

启用/禁用GPS噪声的步骤：

1. 构建任意gazebo目标以生成SDF文件（适用于所有机体）。
   例如：

   ```sh
   make px4_sitl gazebo-classic_iris
   ```

   :::tip
   后续构建不会覆盖SDF文件。
   :::

2. 打开目标机体的SDF文件（例如 **./Tools/simulation/gazebo-classic/sitl_gazebo-classic/models/iris/iris.sdf**）。
3. 查找 `gpsNoise` 元素：

   ```xml
   <plugin name='gps_plugin' filename='libgazebo_gps_plugin.so'>
     <robotNamespace/>
     <gpsNoise>true</gpsNoise>
   </plugin>
   ```

   - 如果存在，GPS噪声已启用。
     可通过删除 `<gpsNoise>true</gpsNoise>` 行来禁用。
   - 如果不存在，GPS噪声已禁用。
     可通过在 `gps_plugin` 部分添加 `gpsNoise` 元素（如上所示）来启用。

下次构建/重新启动Gazebo Classic时，将使用新的GPS噪声设置。

## 加载特定世界

PX4 支持多个 [Worlds](../sim_gazebo_classic/worlds.md)，它们存储在 [PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic/worlds](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/worlds)。
默认情况下 Gazebo Classic 会显示一个平坦且无特征的平面，定义于 [empty.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/empty.world)。

您可以通过在 PX4 配置目标中指定世界名称来加载任意世界。

例如，要加载 _warehouse_ 世界，可以按如下方式追加参数：

```sh
make px4_sitl_default gazebo-classic_plane_cam__warehouse
```

::: info
模型名称 (`plane_cam`) 后带有 _两个下划线_，表示使用默认调试器（无调试器）。
详情见 [代码构建 > PX4 Make 构建目标](../dev_setup/building_px4.md#px4-make-build-targets)。
:::

您也可以通过 `PX4_SITL_WORLD` 环境变量指定完整世界路径进行加载。
这在测试尚未包含在 PX4 中的新世界时非常有用。

:::tip
如果加载的世界与地图不匹配，可能需要 [设置世界位置](#设置世界坐标)。
:::

## 设置世界坐标

机体在模拟世界中会在某个GPS模拟位置附近生成，但不会精确位于Gazebo的坐标原点(0,0,0)，而是会使用一个微小偏移量，这有助于暴露一些常见的代码问题。

::: info
如果使用了模拟真实地理位置的世界文件(例如特定机场)，可能会导致模拟世界显示效果与地面站地图之间出现明显的不匹配现象。为解决这个问题，可以将世界坐标原点设置为"现实"中对应的GPS坐标。
:::

::: info
你也可以设置[自定义起飞位置](#custom_takeoff_location)来实现相同的效果。不过通过地图添加位置更为简便(必要时仍可通过自定义位置覆盖)。
:::

世界坐标在**.world**文件中通过`spherical_coordinates`标签定义。必须同时指定纬度、经度和海拔高度(这三项为必填项)。

示例可参考 [sonoma_raceway.world](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/worlds/sonoma_raceway.world) 文件：

```xml
    <spherical_coordinates>
      <surface_model>EARTH_WGS84</surface_model>
      <latitude_deg>38.161479</latitude_deg>
      <longitude_deg>-122.454630</longitude_deg>
      <elevation>488.0</elevation>
    </spherical_coordinates>
```

你可以通过以下`make`命令在[Sonoma Raceway World](../sim_gazebo_classic/worlds.md#sonoma-raceway)中生成车体进行测试(注意首次生成耗时较长，因为需要从模型库下载模型)：

```sh
make px4_sitl gazebo-classic_rover__sonoma_raceway
```

下方视频展示了环境坐标与真实世界的对齐效果：

<lite-youtube videoid="-a2WWLni5do" title="Driving a simulated PX4 Rover in the Sonoma Raceway"/>

## 分别启动 Gazebo 和 PX4

对于长时间开发会话来说，单独启动 Gazebo Classic 和 PX4 或者从 IDE 内部启动可能会更加方便。

除了现有通过 `sitl_run.sh` 运行并指定参数的 cmake 目标，它会为 px4 加载正确模型，还创建了一个名为 `px4_<mode>` 的启动器目标，该目标是原始 sitl px4 应用程序的轻量级包装器。
这个轻量级包装器仅嵌入应用程序参数，如当前工作目录和模型文件路径。

要分别启动 Gazebo Classic 和 PX4:

- 通过终端指定 `_ide` 变体运行 gazebo classic（或任何其他模拟器）服务器和客户端查看器：

  ```sh
  make px4_sitl gazebo-classic___ide
  ```

  或

  ```sh
  make px4_sitl gazebo-classic_iris_ide
  ```

- 在 IDE 中选择你想要调试的 `px4_<mode>` 目标（例如 `px4_iris`）
- 从 IDE 直接启动调试会话

这种方法显著减少了调试周期时间，因为模拟器始终在后台运行，你只需重新运行非常轻量的 px4 进程。

## 模拟测绘相机

_Gazebo Classic_ 测绘相机模拟一个[MAVLink相机](https://mavlink.io/en/services/camera.html)，可拍摄带地理标记的JPEG图像，并将相机捕获信息发送到连接的地面站。该相机还支持视频流传输，可用于测试测绘任务中的相机捕获功能。

每次捕获图像时，相机都会发送[CAMERA_IMAGE_CAPTURED](https://mavlink.io/en/messages/common.html#CAMERA_IMAGE_CAPTURED)消息。捕获的图像保存在：`PX4-Autopilot/build/px4_sitl_default/src/modules/simulation/simulator_mavlink/frames/DSC_n.jpg`（其中_n_从00000开始，每次捕获递增1）。要模拟搭载该相机的飞机：

```sh
make px4_sitl_default gazebo-classic_plane_cam
```

::: info
该相机还支持/响应以下MAVLink命令：[MAV_CMD_REQUEST_CAMERA_CAPTURE_STATUS](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_CAMERA_CAPTURE_STATUS)（请求相机捕获状态）、[MAV_CMD_REQUEST_STORAGE_INFORMATION](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_STORAGE_INFORMATION)（请求存储信息）、[MAV_CMD_REQUEST_CAMERA_SETTINGS](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_CAMERA_SETTINGS)（请求相机设置）、[MAV_CMD_REQUEST_CAMERA_INFORMATION](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_CAMERA_INFORMATION)（请求相机信息）、[MAV_CMD_RESET_CAMERA_SETTINGS](https://mavlink.io/en/messages/common.html#MAV_CMD_RESET_CAMERA_SETTINGS)（重置相机设置）、[MAV_CMD_STORAGE_FORMAT](https://mavlink.io/en/messages/common.html#MAV_CMD_STORAGE_FORMAT)（存储格式化）、[MAV_CMD_SET_CAMERA_ZOOM](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_ZOOM)（设置相机变焦）、[MAV_CMD_IMAGE_START_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_START_CAPTURE)（开始图像捕获）、[MAV_CMD_IMAGE_STOP_CAPTURE](https://mavlink.io/en/messages/common.html#MAV_CMD_IMAGE_STOP_CAPTURE)（停止图像捕获）、[MAV_CMD_REQUEST_VIDEO_STREAM_INFORMATION](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_VIDEO_STREAM_INFORMATION)（请求视频流信息）、[MAV_CMD_REQUEST_VIDEO_STREAM_STATUS](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_VIDEO_STREAM_STATUS)（请求视频流状态）、[MAV_CMD_SET_CAMERA_MODE](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_CAMERA_MODE)（设置相机模式）。
:::

::: info
模拟相机的实现位于[PX4/PX4-SITL_gazebo-classic/main/src/gazebo_camera_manager_plugin.cpp](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/src/gazebo_camera_manager_plugin.cpp)。
:::

## 模拟深度相机

_Gazebo Classic_ [深度相机模型](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/main/models/depth_camera/depth_camera.sdf.jinja) 使用 [Openni Kinect plugin](https://classic.gazebosim.org/tutorials?tut=ros_gzplugins#OpenniKinect) 模拟 Intel® RealSense™ D455 立体深度相机。

这会分别在 `/camera/depth/image_raw` 和 `/camera/depth/camera_info` ROS 主题上发布深度图像和相机信息。

要使用这些图像，您需要安装 ROS 或 ROS 2。
注意本页面顶部关于在安装 ROS 和 Gazebo 时"避免安装冲突"的警告。

您可以模拟一个带有向前深度相机的四旋翼：

```sh
make px4_sitl gazebo-classic_iris_depth_camera
```

或一个带有向下深度相机的四旋翼：

```sh
make px4_sitl gazebo-classic_iris_downward_depth_camera
```

## 模拟降落伞/飞行终止

_Gazebo Classic_ 可用于模拟在[飞行终止](../advanced_config/flight_termination.md)期间部署[降落伞](../peripherals/parachute.md)（飞行终止由 _Gazebo Classic_ 中模拟的PWM命令触发）。

`if750a` 目标为机体附加了降落伞。
要模拟机体运行，执行以下命令：

```sh
make px4_sitl gazebo-classic_if750a
```

要使机体进入飞行终止状态，可通过强制触发[安全检查](../config/safety.md)失败（该检查设置飞行终止为安全措施）来实现。
例如，可以通过强制[地理围栏违规](../config/safety.md#geofence-failsafe)来触发。

更多信息请参阅：

- [Flight Termination](../advanced_config/flight_termination.md)
- [Parachute](../peripherals/parachute.md)
- [Safety Configuration (Failsafes)](../config/safety.md)

## 视频流传输

PX4 SITL for Gazebo Classic 支持从连接到模拟机体模型的摄像头传感器进行UDP视频流传输。  
当启用流传输时，可以通过_QGroundControl_（使用UDP端口5600）连接到该流，并查看Gazebo Classic环境中模拟机体的视频——就像使用真实摄像头一样。  
视频通过_gstreamer_管道传输，可以在Gazebo Classic UI中通过按钮启用或禁用。

以下框架支持/启用了模拟摄像头传感器：

- [Typhoon H480](#typhoon_h480)

### 先决条件

视频流传输需要_Gstreamer 1.0_。  
所需依赖项在安装Gazebo Classic时应该已经[安装](#安装)（它们包含在标准PX4安装脚本/说明中，适用于macOS和Ubuntu Linux）。

::: info
仅供了解，依赖项包括：`gstreamer1.0-plugins-base`，`gstreamer1.0-plugins-good`，`gstreamer1.0-plugins-bad`，`gstreamer1.0-plugins-ugly`，`libgstreamer-plugins-base1.0-dev`。
:::

### 启动/停止视频流传输

当目标机体支持时，视频流传输会自动启动。  
例如，要通过Typhoon H480启动视频流传输：

```sh
make px4_sitl gazebo-classic_typhoon_h480
```

可以通过Gazebo UI中的_Video ON/OFF_按钮暂停/重新启动流传输。

![视频开启/关闭按钮](../../assets/simulation/gazebo_classic/sitl_video_stream.png)

### 如何查看Gazebo视频

查看SITL/Gazebo Classic摄像头视频流的最简单方法是在_QGroundControl_中进行操作。  
只需打开 **Application Settings > General**，将 **Video Source** 设置为_UDP h.264 Video Stream_，并将 **UDP Port** 设置为_5600_：

![QGC Gazebo视频流设置](../../assets/simulation/gazebo_classic/qgc_gazebo_video_stream_udp.png)

Gazebo Classic的视频随后应在_QGroundControl_中显示，就像真实摄像头一样。

![QGC Gazebo视频流示例](../../assets/simulation/gazebo_classic/qgc_gazebo_video_stream_typhoon.jpg)

::: info
Typhoon世界场景并不特别有趣。
:::

也可以通过_Gstreamer Pipeline_查看视频。  
只需在终端中输入以下命令：

```sh
gst-launch-1.0  -v udpsrc port=5600 caps='application/x-rtp, media=(string)video, clock-rate=(int)90000, encoding-name=(string)H264' \
! rtph264depay ! avdec_h264 ! videoconvert ! autovideosink fps-update-interval=1000 sync=false
```

### 详细日志记录

当模型出现异常时，SITL会静默失败。  
可以通过`VERBOSE_SIM`启用更详细的日志记录，如下所示：

```sh
export VERBOSE_SIM=1
make px4_sitl gazebo-classic
```

或

```sh
VERBOSE_SIM=1 make px4_sitl gazebo-classic
```

## Lockstep

PX4 SITL 和 Gazebo-Classic 已配置为以 _lockstep_ 模式运行。
这意味着 PX4 和模拟器以相同的速度运行，因此可以对传感器和执行器消息做出适当响应。
Lockstep 模式使得可以 [更改模拟速度](#修改仿真速度)，也可以暂停以逐行调试代码。

#### Lockstep 序列

Lockstep 的步骤顺序为：

1. 模拟器发送包含时间戳 `time_usec` 的传感器消息 [HIL_SENSOR](https://mavlink.io/en/messages/common.html#HIL_SENSOR)，用于更新 PX4 的传感器状态和时间。
1. PX4 接收该消息后进行一次状态估计、控制等操作，并最终发送执行器消息 [HIL_ACTUATOR_CONTROLS](https://mavlink.io/en/messages/common.html#HIL_ACTUATOR_CONTROLS)。
1. 模拟器等待接收执行器/电机消息后，再模拟物理过程并计算下一个要发送给 PX4 的传感器消息。

系统启动时会有一个 "freewheeling" 阶段，模拟器发送包含时间的传感器消息，因此 PX4 会运行直到初始化完成并发送执行器消息。

#### 禁用 Lockstep

如果例如 SITL 需要与不支持此功能的模拟器配合使用，可以禁用 lockstep 模拟。
在这种情况下，模拟器和 PX4 将使用主机系统时间，不再相互等待。

在以下位置禁用 lockstep：

- PX4 中，运行 `make px4_sitl_default boardconfig` 并设置位于 toolchain 下的 `BOARD_NOLOCKSTEP` "强制禁用 lockstep" 符号。
- Gazebo Classic 中，编辑 [模型 SDF 文件](https://github.com/PX4/PX4-SITL_gazebo-classic/blob/3062d287c322fabf1b41b8e33518eb449d4ac6ed/models/plane/plane.sdf#L449)，设置 `<enable_lockstep>false</enable_lockstep>`。

## 扩展与定制

要扩展或定制仿真接口，请编辑 `Tools/simulation/gazebo/sitl_gazebo` 文件夹中的文件。
代码可在Github上的 [sitl_gazebo repository](https://github.com/PX4/PX4-SITL_gazebo) 获得。

::: info
构建系统会强制使用正确的GIT子模块，包括模拟器。
它不会覆盖目录中文件的更改。
:::

## 更多信息

- [ROS与Gazebo Classic仿真](../simulation/ros_interface.md)
- [Gazebo Classic Octomap](../sim_gazebo_classic/octomap.md)