# Gazebo模拟

:::warning
Gazebo之前称为"Gazebo Ignition"（而_Gazebo Classic_之前称为Gazebo）。
更多信息请参见[官方博客文章](https://www.openrobotics.org/blog/2022/4/6/a-new-era-for-gazebo)。
:::

[Gazebo](https://gazebosim.org/home)是一个开源的机器人模拟器。
它取代了旧版的[Gazebo Classic](../sim_gazebo_classic/index.md)模拟器，并是Ubuntu 22.04及后续版本唯一支持的Gazebo版本。

**支持的飞行器:** 四旋翼无人机、固定翼飞机、垂直起降飞行器、地面探测车

<lite-youtube videoid="eRzdGD2vgkU" title="PX4 SITL Ignition Gazebo Tunnel Environment"/>

::: info
更多信息请参见[模拟](../simulation/index.md)，了解关于模拟器、模拟环境和模拟配置（例如支持的飞行器）的通用信息。
:::

## 安装 (Ubuntu Linux)

Gazebo Harmonic 默认包含在 Ubuntu 22.04 的正常[开发环境配置](../dev_setup/dev_env_linux_ubuntu.md#simulation-and-nuttx-pixhawk-targets)中。

:::info
PX4 安装脚本基于以下说明：[Ubuntu 上的二进制安装](https://gazebosim.org/docs/harmonic/install_ubuntu/) (gazebosim.org)
:::

::: warning
Ubuntu 20.04 及更早版本无法安装 Gazebo Harmonic。

在 Ubuntu 20.04 上我们建议使用 [Gazebo Classic](../sim_gazebo_classic/index.md)。
如果必须使用 Gazebo，请升级到 Ubuntu 22.04。

截至 2024 年 11 月，仍可在 Ubuntu 20.04 上[安装 Gazebo Garden](https://gazebosim.org/docs/garden/install_ubuntu/)。
此后 Garden 将达到生命周期终点，不应再使用。
:::

## 运行仿真

以下命令可用于在 Gazebo 中运行 PX4 SITL 仿真：

```sh
cd /path/to/PX4-Autopilot
make px4_sitl gz_x500
```

### 支持的机体模型

| 机体            | 命令                          | PX4 系统启动 ID |
|-----------------|-------------------------------|----------------|
| 四旋翼飞行器(x500) | `make px4_sitl gz_x500`       | 4001           |
| 四旋翼飞行器(x500_aruco) | `make px4_sitl gz_x500_aruco` | 4002           |
| 四旋翼飞行器(x500_baylands) | `make px4_sitl gz_x500_baylands` | 4003         |
| 四旋翼飞行器(x500_lawn) | `make px4_sitl gz_x500_lawn`   | 4004           |
| 四旋翼飞行器(x500_rover) | `make px4_sitl gz_x500_rover`  | 4005           |
| 四旋翼飞行器(x500_walls) | `make px4_sitl gz_x500_walls`  | 4006           |
| 四旋翼飞行器(x500_windy) | `make px4_sitl gz_x500_windy`  | 4007           |
| 四旋翼飞行器(x500_moving_platform) | `make px4_sitl gz_x500_moving_platform` | 4008 |
| 四旋翼飞行器(x500_default) | `make px4_sitl gz_x500_default` | 4009         |

::: info
更多机体模型请参见 [Gazebo 模型仓库](../sim_gazebo_gz/gazebo_models.md)。
:::

### 独立模式

在独立模式下，Gazebo 与 PX4 仿真引擎分离运行：

```sh
PX4_GZ_STANDALONE=1 make px4_sitl gz_x500
```

### 无头模式

无头模式（无图形界面）可加快仿真速度：

```sh
make px4_sitl_nolockstep gz_x500
```

### 自定义起飞位置

通过环境变量设置自定义地理坐标：

```sh
export PX4_HOME_LAT=51.1788
export PX4_HOME_LON=-1.8263
export PX4_HOME_ALT=101
make px4_sitl gz_x500
```

### 指定仿真世界

通过模型名称后缀或环境变量指定仿真世界：

```sh
# 通过命令指定
make px4_sitl gz_x500_windy

# 通过环境变量指定
PX4_GZ_WORLD=windy make px4_sitl gz_x500
```

| 世界名称        | 命令                          | 描述                         |
|-----------------|-------------------------------|------------------------------|
| `default`       | `make px4_sitl *`             | 空白世界（灰色平面）         |
| `aruco`         | `make px4_sitl *_aruco`       | 带 ArUco 标记的空白世界      |
| `baylands`      | `make px4_sitl *_baylands`    | 周围是水域的世界             |
| `lawn`          | `make px4_sitl *_lawn`        | 测试地面车辆的草坪世界       |
| `rover`         | `make px4_sitl *_rover`       | 优化版地面车辆测试世界       |
| `walls`         | `make px4_sitl *_walls`       | 测试防撞的世界               |
| `windy`         | `make px4_sitl *_windy`       | 启用风力的空白世界           |
| `moving_platform` | `make px4_sitl *_moving_platform` | 带移动起降平台的世界       |

::: warning
默认世界为 `default`，但不要在模型后缀中显式使用 `_default`，否则 PX4 将无法启动。应使用 `make px4_sitl gz_x500` 而不是 `make px4_sitl gz_x500_default`。
:::

### 调整仿真速度

通过 `PX4_SIM_SPEED_FACTOR` 环境变量控制仿真速度：

```sh
# 两倍速运行
PX4_SIM_SPEED_FACTOR=2 make px4_sitl gz_x500

# 半速运行
PX4_SIM_SPEED_FACTOR=0.5 make px4_sitl gz_x500
```

::: info
仿真速度受硬件限制，高性能台式机通常可达 6-10 倍速，笔记本可达 3-4 倍速。
:::

::: info
仿真以 "lockstep" 模式运行，Gazebo 与 PX4 同步速度。此模式支持代码步进调试，但无法在 Gazebo 中禁用。
:::

### 常见问题

::: info
`baylands` 世界在 Gazebo Harmonic 中会因网格过多触发警告，可忽略以下提示：

```sh
[Wrn] [SDFFeatures.cc:843] The geometry element of collision [collision] couldn't be created
```

:::

## 使用/配置选项

启动流程允许高度灵活的配置。
具体来说，可以：

- 使用任意世界启动新仿真或连接到已运行的仿真。
- 向仿真中添加新机体或将新PX4实例链接到现有实例。

这些场景通过设置相应的环境变量进行管理。

### 语法

启动语法形式如下：

```sh
ARGS ./build/px4_sitl_default/bin/px4
```

其中 `ARGS` 是环境变量列表，包括：

- `PX4_SYS_AUTOSTART` (**必填**):
  设置要启动的PX4机体的[自动启动ID](../dev_airframes/adding_a_new_frame.md)。

- `PX4_GZ_MODEL_NAME`:
  设置Gazebo仿真中一个_已存在_模型的名称。
  如果提供，启动脚本将尝试将新PX4实例绑定到与该名称完全匹配的Gazebo资源。

  - 与 `PX4_SIM_MODEL` 互斥。

- `PX4_SIM_MODEL`:
  设置要在仿真器中生成的新Gazebo模型名称。
  如果提供，启动脚本将在Gazebo资源路径中查找匹配该变量的模型，生成它并将新PX4实例绑定到该模型。

  - 与 `PX4_GZ_MODEL_NAME` 互斥。

  ::: info
  环境变量 `PX4_GZ_MODEL` 已被弃用，其功能已合并到 `PX4_SIM_MODEL` 中。
  :::

- `PX4_GZ_MODEL_POSE`:
  当采用 `PX4_SIM_MODEL` 时设置模型的生成位置和姿态。
  如果提供，启动脚本将按照 `"x,y,z,roll,pitch,yaw"` 语法生成模型，其中位置单位为米，角度单位为弧度。

  - 如果省略，将使用零姿态 `[0,0,0,0,0,0]`。
  - 如果提供的值少于6个，缺失值将固定为零。
  - 该参数只能与 `PX4_SIM_MODEL` 一起使用（不能与 `PX4_GZ_MODEL_NAME` 一起使用）。

- `PX4_GZ_WORLD`:
  设置新仿真的Gazebo世界文件。
  如果未提供，则使用[默认](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/default.sdf)。

  - 如果已有仿真正在运行，此变量将被忽略。
  - 此值应[为所选机体指定](#adding-new-worlds-and-models)，但可通过此参数覆盖。
  - 如果选择[移动平台世界](../sim_gazebo_gz/worlds.md#moving-platform)使用 `PX4_GZ_WORLD=moving_platform`（或任何使用移动平台插件的世界），可通过环境变量配置平台：
    - `PX4_GZ_PLATFORM_VEL`: 平台速度（m/s）。
    - `PX4_GZ_PLATFORM_HEADING_DEG`: 平台航向和速度方向（度）。0 = 东，正方向为逆时针。

- `PX4_SIMULATOR=GZ`:
  设置仿真器，对于Gazebo必须为 `gz`。

  - 此值应[为所选机体设置](#adding-new-worlds-and-models)，在这种情况下无需作为参数设置。

- `PX4_GZ_STANDALONE`:
  告知PX4不应启动Gazebo实例。
  需要单独启动Gazebo，如[独立模式](#standalone-mode)中所述。

- `PX4_GZ_SIM_RENDER_ENGINE`:
  设置Gazebo使用的渲染引擎。

  默认渲染引擎（OGRE 2）在某些平台/环境中支持不佳。
  如果在虚拟机上运行PX4时遇到渲染问题，可通过指定 `PX4_GZ_SIM_RENDER_ENGINE=ogre` 将渲染引擎设置为OGRE 1。

- `PX4_SIM_SPEED_FACTOR`:
  设置仿真运行速度因子[快于/慢于实时](#change-simulation-speed)。

- `PX4_GZ_FOLLOW_OFFSET_X`, `PX4_GZ_FOLLOW_OFFSET_Y`, `PX4_GZ_FOLLOW_OFFSET_Z`:
  设置跟随摄像机相对于机体的相对偏移。

PX4 Gazebo 世界和模型数据库[可在GitHub上找到](https://github.com/PX4/PX4-gazebo-models)。

::: info
`gz_env.sh.in` 被编译并放置在 `$PX4_DIR/build/px4_sitl_default/rootfs/gz_env.sh`
:::

### 示例

以下是上述不同场景的示例。

1. **启动仿真 + 默认世界 + 默认姿态生成机体**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

2. **启动仿真 + 默认世界 + 自定义姿态生成机体 (y=2m)**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,2" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

3. **启动仿真 + 默认世界 + 链接到现有机体**

   ```sh
   PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_NAME=x500 ./build/px4_sitl_default/bin/px4
   ```

4. **以独立模式启动仿真 + 连接到运行默认世界的Gazebo实例**

   ```sh
   PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4
   ```

   在另一个终端运行：

   ```sh
   python /path/to/simulation-gazebo
   ```

## 添加新世界和模型

SDF文件、网格文件、纹理以及与Gazebo中世界和模型功能及外观相关的内容可以放置在[PX4-gazebo-models](https://github.com/PX4/PX4-gazebo-models)中的相应`/worlds`和`/models`目录内。

在PX4中，按照以下步骤添加模型和世界：

### 添加模型

要添加新模型：

1. 定义[机体配置文件](../dev_airframes/adding_a_new_frame.md)。
1. 在机体配置文件中定义Gazebo的默认参数（此示例来自[x500四旋翼](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/4001_gz_x500))：

   ```ini
   PX4_SIMULATOR=${PX4_SIMULATOR:=gz}
   PX4_GZ_WORLD=${PX4_GZ_WORLD:=default}
   PX4_SIM_MODEL=${PX4_SIM_MODEL:=<your model name>}
   ```

   - `PX4_SIMULATOR=${PX4_SIMULATOR:=gz}` 为特定机体设置默认模拟器（Gz）。
   - `PX4_GZ_WORLD=${PX4_GZ_WORLD:=default}` 为特定机体设置[默认世界](https://github.com/PX4/PX4-gazebo-models/blob/main/worlds/default.sdf)。

   - 设置`PX4_SIM_MODEL`的默认值后，可以通过以下方式启动模拟：

     ```sh
     PX4_SYS_AUTOSTART=<your new airframe id> ./build/px4_sitl_default/bin/px4
     ```

1. 为[机体](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/airframes/CMakeLists.txt)添加CMake目标。

   - 如果计划使用"regular"模式，将模型SDF添加到`Tools/simulation/gz/models/`。
   - 如果计划使用_standalone_模式，将模型SDF添加到`~/.simulation-gazebo/models/`。

   你也可以同时使用两者。

### 添加世界

要添加新世界：

1. 将你的世界添加到此处的[CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/gz_bridge/CMakeLists.txt)中的世界列表。
   这是必须的，以便`CMake`生成正确的目标。

   - 如果计划使用"normal"模式，将你的世界SDF添加到`Tools/simulation/gz/worlds/`。
   - 如果计划使用_standalone_模式，将你的世界SDF添加到`~/.simulation-gazebo/worlds/`

::: info
只要世界文件和模型文件位于Gazebo搜索路径(`GZ_SIM_RESOURCE_PATH`)中，就不需要将它们添加到PX4的世界和模型目录中。
但是，`make px4_sitl gz_<model>_<world>`将无法使用它们。
:::

## 通过插件扩展 Gazebo

世界、机体（模型）和传感器行为可以通过插件进行自定义。  
更多信息请参见 [Gazebo 插件](../sim_gazebo_gz/plugins.md)。

## 多机体模拟

多机体模拟支持在Linux主机上运行。

更多信息请参见: [多机体模拟与Gazebo](../sim_gazebo_gz/multi_vehicle_simulation.md)

## 更多信息

- [px4-simulation-ignition](https://github.com/Auterion/px4-simulation-ignition)