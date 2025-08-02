

# Gazebo 仿真

:::warning
Gazebo 以前被称为 "Gazebo Ignition"（而 _Gazebo Classic_ 以前被称为 Gazebo）。
详见[官方博客文章](https://www.openrobotics.org/blog/2022/4/6/a-new-era-for-gazebo)获取更多信息。
:::

[Gazebo](https://gazebosim.org/home) 是一个开源机器人模拟器。  
它取代了旧版的 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟器，是 Ubuntu 22.04 及后续版本唯一支持的 Gazebo 版本。

**支持的机体类型：** 四旋翼、飞行器、垂直起降、地面车

<lite-youtube videoid="eRzdGD2vgkU" title="PX4 SITL Ignition Gazebo 隧道环境"/>

::: info
有关模拟器、模拟环境和模拟配置（例如支持的机体）的通用信息，请参见 [Simulation](../simulation/index.md)。
:::

## 安装 (Ubuntu Linux)

Gazebo Harmonic 默认安装在 Ubuntu 22.04 系统中，作为正常 [开发环境设置](../dev_setup/dev_env_linux_ubuntu.md#simulation-and-nuttx-pixhawk-targets) 的组成部分。

:::info
PX4 安装脚本基于以下指南：[Ubuntu上的二进制安装](https://gazebosim.org/docs/harmonic/install_ubuntu/) (gazebosim.org)。
:::

::: warning
Gazebo Harmonic 无法在 Ubuntu 20.04 及更早版本上安装。

在 Ubuntu 20.04 上我们建议使用 [Gazebo Classic](../sim_gazebo_classic/index.md)。
如果必须使用 Gazebo，建议升级到 Ubuntu 22.04。

在 2024 年 11 月之前，仍可在 Ubuntu 20.04 上 [安装 Gazebo Garden](https://gazebosim.org/docs/garden/install_ubuntu/)。
此后 Garden 将达到生命周期终点，不应继续使用。
:::

## 运行模拟

可使用以下 `make` 命令便捷地运行 Gazebo SITL 模拟：

```sh
cd /path/to/PX4-Autopilot
make px4_sitl gz_x500
```

这将同时运行 PX4 SITL 实例和 Gazebo 客户端。

支持的机体和 `make` 命令如下所示。
请注意所有 gazebo make 目标均以 `gz_` 为前缀。

| 机体                                                                                                                       | 命令                             | `PX4_SYS_AUTOSTART` |
| ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- | ------------------- |
| [Quadrotor (x500)](../sim_gazebo_gz/vehicles.md#x500-quadrotor)                                                               | `make px4_sitl gz_x500`             | 4001                |
| [X500 Quadrotor with Depth Camera (Front-facing)](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-depth-camera-front-facing) | `make px4_sitl gz_x500_depth`       | 4002                |
| [Quadrotor(x500) with Vision Odometry](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-visual-odometry)                      | `make px4_sitl gz_x500_vision`      | 4005                |
| [Quadrotor(x500) with 1D LIDAR (Down-facing)](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-1d-lidar-down-facing)          | `make px4_sitl gz_x500_lidar_down`  | 4016                |
| [Quadrotor(x500) with 2D LIDAR](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-2d-lidar)                                    | `make px4_sitl gz_x500_lidar_2d`    | 4013                |
| [Quadrotor(x500) with 1D LIDAR (Front-facing)](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-1d-lidar-front-facing)        | `make px4_sitl gz_x500_lidar_front` | 4017                |
| [Quadrotor(x500) with gimbal (Front-facing) in Gazebo](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-gimbal-front-facing)  | `make px4_sitl gz_x500_gimbal`      | 4019                |
| [VTOL](../sim_gazebo_gz/vehicles.md#standard-vtol)                                                                            | `make px4_sitl gz_standard_vtol`    | 4004                |
| [Plane](../sim_gazebo_gz/vehicles.md#standard-plane)                                                                          | `make px4_sitl gz_rc_cessna`        | 4003                |
| [Advanced Plane](../sim_gazebo_gz/vehicles.md#advanced-plane)                                                                 | `make px4_sitl gz_advanced_plane`   | 4008                |
| [Differential Rover](../sim_gazebo_gz/vehicles.md#differential-rover)                                                         | `make px4_sitl gz_r1_rover`         | 4009                |
| [Ackermann Rover](../sim_gazebo_gz/vehicles.md#ackermann-rover)                                                               | `make px4_sitl gz_rover_ackermann`  | 4012                |
| [Quad Tailsitter VTOL](../sim_gazebo_gz/vehicles.md#quad-tailsitter-vtol)                                                     | `make px4_sitl gz_quadtailsitter`   | 4018                |
| [Tiltrotor VTOL](../sim_gazebo_gz/vehicles.md#tiltrotor-vtol)                                                                 | `make px4_sitl gz_tiltrotor`        | 4020                |

所有 [机体模型](../sim_gazebo_gz/vehicles.md)（以及 [世界](#指定世界)）均作为子模块包含在 [Gazebo 模型仓库](../sim_gazebo_gz/gazebo_models.md) 仓库中。

上述命令将启动带有完整 UI 界面的单个机体。
_QGroundControl_ 应能自动连接到模拟的机体。

### 独立模式

Gazebo SITL 的另一种连接方式是通过 _独立模式_。  
在此模式下，PX4 SITL 和 Gazebo 会分别在各自的终端中启动。  
默认情况下这些终端位于同一主机上，但你也可以连接网络中任意两台设备上运行的 SITL 和 Gazebo 实例（甚至可以通过 VPN 连接不同网络）。

通过在 `make` 命令前添加 `PX4_GZ_STANDALONE=1` 前缀来启动 PX4 的独立模式：

```sh
cd /path/to/PX4-Autopilot
PX4_GZ_STANDALONE=1 make px4_sitl gz_x500
```

随后 PX4 SITL 会等待直到检测到一个 _gz-server_ 实例，并连接到它。

::: info
如果你在运行 `make` 命令时尚未启动 _gz-server_，你会看到以下警告，直到启动 Gazebo 并被 PX4 检测到 _gz-server_ 实例：

```sh
WARN [gz bridge] Service call timed out as Gazebo has not been detected
```

:::

启动仿真最简单的方式是使用 Python 脚本 [simulation-gazebo](https://github.com/PX4/PX4-gazebo-models/blob/main/simulation-gazebo)，该脚本可在 [Gazebo Models Repository](../sim_gazebo_gz/gazebo_models.md) 仓库中找到。  
此脚本可用于启动带有任何支持世界和机体的 _gz-server_ 实例。

该脚本无需安装额外依赖即可使用，首次使用时会默认获取支持的 PX4 模型和世界并保存到 `~/.simulation-gazebo`。  
再次调用脚本时会使用此目录获取模型和世界。因此，如果你想使用自己的模型并以独立模式运行，必须将其源代码放置在 `~/.simulation-gazebo` 中。

你可以使用任意方法（例如 `wget`）将脚本本地获取：

```sh
wget https://raw.githubusercontent.com/PX4/PX4-gazebo-models/main/simulation-gazebo
```

脚本可通过以下方式启动：

```sh
cd /path/to/script/
python3 simulation-gazebo
```

更多信息和参数请参见 [Gazebo Models](../sim_gazebo_gz/gazebo_models.md)。

::: info
如果 `make px4_sitl gz_x500` 返回错误 `ninja: error: unknown target 'gz_x500'`，则运行 `make distclean` 以从干净状态重新开始，然后再次尝试运行 `make px4_sitl gz_x500`。
:::

### 无头模式

您可能希望以 "headless mode"（无头模式）运行 Gazebo（不使用 Gazebo 图形界面），因为它消耗的资源更少，且不依赖于您的系统是否拥有支持 OpenGL 渲染的显卡。这会使加载和运行速度更快，对于许多简单使用场景来说可能已经足够。

可以通过在命令前添加 `HEADLESS=1` 环境变量来以无头模式运行模拟：

```sh
HEADLESS=1 make px4_sitl gz_x500
```

### 设置自定义起飞位置  
在 Gazebo Classic 中，可以通过环境变量设置起飞位置。  

需要设置的变量为：PX4_HOME_LAT、PX4_HOME_LON 和 PX4_HOME_ALT。  

例如：  

```
export PX4_HOME_LAT=51.1788  
export PX4_HOME_LON=-1.8263  
export PX4_HOME_ALT=101  
make px4_sitl gz_x500  
```

### 指定世界

可以通过将所需世界连接到所需机体的名称后运行特定世界中的模拟。
例如，要使用 `x500` 机体运行风场世界，可以指定：

```sh
make px4_sitl gz_x500_windy
```

也可以通过 `PX4_GZ_WORLD` 环境变量指定世界：

```sh
PX4_GZ_WORLD=windy make px4_sitl gz_x500
```

[支持的世界](../sim_gazebo_gz/worlds.md) 列表如下。

| 世界             | 命令                            | 描述                                                 |
| ----------------- | ---------------------------------- | ----------------------------------------------------------- |
| `default`         | `make px4_sitl *`                  | 空世界（灰色平面）                                  |
| `aruco`           | `make px4_sitl *_aruco`            | 空世界带有 aruco 标记用于测试精确着陆 |
| `baylands`        | `make px4_sitl *_baylands`         | 被水域环绕的 Baylands 世界                          |
| `lawn`            | `make px4_sitl *_lawn`             | 用于测试地面机器人的草坪世界                       |
| `rover`           | `make px4_sitl *_rover`            | 机器人世界（优化/首选）                           |
| `walls`           | `make px4_sitl *_walls`            | 用于测试防撞的世界                                  |
| `windy`           | `make px4_sitl *_windy`            | 启用风场的空世界                                 |
| `moving_platform` | `make px4_sitl *_moving_platform`  | 带有移动起降平台的世界                           |

:::warning
请注意，如果未指定世界，PX4 将使用 `default` 世界。
但您不应在模型上显式指定 `_default`，这将阻止 PX4 启动。
换句话说，使用 `make px4_sitl gz_x500` 而不是 `make px4_sitl gz_x500_default` 来使用默认世界。
:::

::: info
Baylands 世界在 Gazebo Harmonic 中会触发警告，因为存在大量网格。
可以忽略该警告：

```sh
[Wrn] [SDFFeatures.cc:843] The geometry element of collision [collision] couldn't be created
```

:::

### 调整模拟速度

当使用Gazebo时，PX4 SITL 可以以比实时更快或更慢的速度运行。

通过环境变量 `PX4_SIM_SPEED_FACTOR` 设置速度因子。  
例如，以两倍实时速度运行X500框架的Gazebo模拟：

```sh
PX4_SIM_SPEED_FACTOR=2 make px4_sitl gz_x500
```

以半实时速度运行：

```sh
PX4_SIM_SPEED_FACTOR=0.5 make px4_sitl gz_x500
```

可通过 `EXPORT` 将该因子应用于当前会话中的所有SITL运行：

```sh
export PX4_SIM_SPEED_FACTOR=2
make px4_sitl gz_x500
```

::: info
最终受限于IO或CPU性能时，系统会自动降低速度。  
高性能台式机通常可以达到6-10倍速，笔记本电脑通常可达到3-4倍速。
:::

::: info
模拟器以 _lockstep_ 模式运行，这意味着Gazebo以与PX4相同的速率运行模拟器（GZBridge在每次模拟步进时通过 `clockCallback` 设置PX4时间）。  
除作为实现快/慢速模拟的先决条件外，该模式还允许您暂停模拟以逐步执行代码。  
Gazebo上无法禁用lockstep模式。
:::

## 使用/配置选项

启动管道支持高度灵活的配置。具体而言，可以：

- 使用任意世界启动新的模拟，或连接到已运行的模拟。
- 向模拟中添加新机体，或将新的PX4实例链接到现有实例。

这些场景通过设置适当的环境变量进行管理。

### 语法

启动语法形式如下：

```sh
ARGS ./build/px4_sitl_default/bin/px4
```

其中 `ARGS` 是一个包含以下环境变量的列表：

- `PX4_SYS_AUTOSTART` (**必填**):  
  设置 PX4 机体的[自动启动 ID](../dev_airframes/adding_a_new_frame.md)。

- `PX4_GZ_MODEL_NAME`:  
  设置 Gazebo 模拟中已存在的模型名称。  
  若提供此参数，启动脚本将尝试将新的 PX4 实例绑定到与该名称完全匹配的 Gazebo 资源上。  

  - 该设置与 `PX4_SIM_MODEL` 互斥。

- `PX4_SIM_MODEL`:  
  设置要在模拟器中生成的新 Gazebo 模型名称。  
  若提供此参数，启动脚本会在 Gazebo 资源路径中查找匹配名称的模型，生成模型并将其绑定到新的 PX4 实例。  

  - 该设置与 `PX4_GZ_MODEL_NAME` 互斥。  

  ::: info  
  环境变量 `PX4_GZ_MODEL` 已被弃用，其功能已合并到 `PX4_SIM_MODEL` 中。  
  :::

- `PX4_GZ_MODEL_POSE`:  
  当采用 `PX4_SIM_MODEL` 时，设置模型的生成位置和方向。  
  若提供此参数，启动脚本将按照 `"x,y,z,roll,pitch,yaw"` 格式生成模型，其中位置单位为米，角度单位为弧度。  

  - 若未提供，则使用零姿态 `[0,0,0,0,0,0]`。  
  - 若提供的参数少于 6 个，缺失部分将固定为零。  
  - 此参数仅能与 `PX4_SIM_MODEL` 配合使用（不能与 `PX4_GZ_MODEL_NAME` 配合使用）。

- `PX4_GZ_WORLD`:  
  设置 Gazebo 新模拟的场景文件。  
  若未提供，则使用[默认场景](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/default.sdf)。  

  - 若已有模拟正在运行，此变量将被忽略。  
  - 此值应[为所选机体指定](#添加新的世界和模型)，但可以通过此参数覆盖。  
  - 若通过 `PX4_GZ_WORLD=moving_platform`（或使用移动平台插件的任何场景）选择[移动平台场景](../sim_gazebo_gz/worlds.md#moving-platform)，则可通过环境变量配置平台：  
    - `PX4_GZ_PLATFORM_VEL`: 平台速度（米/秒）。  
    - `PX4_GZ_PLATFORM_HEADING_DEG`: 平台航向及速度方向（度）。0 表示东，正方向为逆时针。

- `PX4_SIMULATOR=GZ`:  
  设置模拟器，对于 Gazebo 必须设置为 `gz`。  

  - 此值应[为所选机体设置](#添加新的世界和模型)，因此无需作为参数单独设置。

- `PX4_GZ_STANDALONE`:  
  告知 PX4 不启动 Gazebo 实例。  
  需要单独启动 Gazebo，详见[独立模式](#独立模式)。

- `PX4_GZ_SIM_RENDER_ENGINE`:  
  设置 Gazebo 使用的渲染引擎。  

  默认渲染引擎（OGRE 2）在某些平台/环境中支持不佳。  
  若在虚拟机中运行 PX4 时遇到渲染问题，请指定 `PX4_GZ_SIM_RENDER_ENGINE=ogre` 以切换为 OGRE 1 渲染引擎。

- `PX4_SIM_SPEED_FACTOR`:  
  设置模拟的运行速度因子（[快于或慢于实时](#调整模拟速度)）。

- `PX4_GZ_FOLLOW_OFFSET_X`, `PX4_GZ_FOLLOW_OFFSET_Y`, `PX4_GZ_FOLLOW_OFFSET_Z`:  
  设置跟随相机相对于机体的偏移量。

PX4 的 Gazebo 场景和模型数据库[可通过 GitHub 获取](https://github.com/PX4/PX4-gazebo-models)。

::: info  
`gz_env.sh.in` 会被编译并生成到 `$PX4_DIR/build/px4_sitl_default/rootfs/gz_env.sh`  
:::

### 示例

以下是上述不同场景的一些示例：

1. **启动模拟器 + 默认世界 + 在默认姿态下生成机体**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

2. **启动模拟器 + 默认世界 + 在自定义姿态下生成机体(y=2m)**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,2" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

3. **启动模拟器 + 默认世界 + 连接现有机体**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_NAME=x500 ./build/px4_sitl_default/bin/px4
   ```

4. **以独立模式启动模拟器 + 连接到运行默认世界的Gazebo实例**

   ```sh
   PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

   在另一个终端运行：

   ```sh
   python /path/to/simulation-gazebo
   ```

## 添加新的世界和模型

SDF 文件、网格文件、纹理以及与 Gazebo 中世界和模型的功能与外观相关的其他任何内容，都可以放入 [PX4-gazebo-models](https://github.com/PX4/PX4-gazebo-models) 仓库中对应的 `/worlds` 和 `/models` 目录。

在 PX4 中，按照以下步骤添加模型和世界。

### 添加模型

要添加新的模型：

1. 定义一个[机体配置文件](../dev_airframes/adding_a_new_frame.md)。
1. 在机体配置文件中定义Gazebo的默认参数（该示例来自[x500四旋翼](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/4001_gz_x500))：

   ```ini
   PX4_SIMULATOR=${PX4_SIMULATOR:=gz}
   PX4_GZ_WORLD=${PX4_GZ_WORLD:=default}
   PX4_SIM_MODEL=${PX4_SIM_MODEL:=<your model name>}
   ```

   - `PX4_SIMULATOR=${PX4_SIMULATOR:=gz}` 为该特定机体设置默认模拟器（Gz）。
   - `PX4_GZ_WORLD=${PX4_GZ_WORLD:=default}` 为该特定机体设置[默认世界](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/default.sdf)。

   - 设置 `PX4_SIM_MODEL` 的默认值后，您只需执行以下命令即可启动模拟：

     ```sh
     PX4_SYS_AUTOSTART=<your new airframe id> ./build/px4_sitl_default/bin/px4
     ```

1. 为[机体](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/CMakeLists.txt)添加CMake目标。

   - 如果计划使用 "regular" 模式，请将您的模型SDF文件添加到 `Tools/simulation/gz/models/`。
   - 如果计划使用 _standalone_ 模式，请将您的模型SDF文件添加到 `~/.simulation-gazebo/models/`

   当然，您也可以同时使用这两种模式。

### 添加一个世界

要添加新世界:

1. 将你的世界添加到[此处的CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/gz_bridge/CMakeLists.txt)中世界列表。
   这是为了让`CMake`生成正确目标所必需的。

   - 如果计划使用"normal"模式，请将你的世界SDF文件添加到`Tools/simulation/gz/worlds/`目录。
   - 如果计划使用_standalone_模式，请将你的世界SDF文件添加到`~/.simulation-gazebo/worlds/`目录

::: info
只要世界文件和模型文件位于Gazebo搜索路径(`GZ_SIM_RESOURCE_PATH`)中，就不需要将它们添加到PX4世界和模型目录。
但`make px4_sitl gz_<model>_<world>`命令将无法使用它们。
:::

## 通过插件扩展 Gazebo

世界、机体（模型）和传感器行为可以通过插件进行自定义。  
详情请参见 [Gazebo 插件](../sim_gazebo_gz/plugins.md)。

## 多机体模拟

多机体模拟支持在Linux主机上运行。

欲了解更多信息，请参见：[多机体模拟与Gazebo](../sim_gazebo_gz/multi_vehicle_simulation.md)

## 更多信息

- [px4-simulation-ignition](https://github.com/Auterion/px4-simulation-ignition)