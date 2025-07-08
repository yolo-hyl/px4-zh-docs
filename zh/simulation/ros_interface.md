# ROS 与 Gazebo Classic 模拟

[ROS](../ros/index.md) (机器人操作系统) 可与 PX4 和 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟器配合使用。
它通过 [MAVROS](../ros/mavros_installation.md) MAVLink 节点与 PX4 进行通信。

ROS/Gazebo Classic 与 PX4 的集成遵循下图所示模式（该图展示了通用的 [PX4 模拟环境](../simulation/index.md#sitl-simulation-environment)）。
PX4 与模拟器（如 Gazebo Classic）通信以接收模拟世界的传感器数据并发送电机和执行器值。
它与地面站和外部 API（如 ROS）通信以发送模拟环境的遥测数据并接收命令。

![PX4 SITL 概览](../../assets/simulation/px4_sitl_overview.png)

::: info
与"正常行为"唯一的_细微_差别是 ROS 会主动连接 14557 端口，而外部 API 通常在 UDP 14540 端口监听连接。
:::

## 安装 ROS 和 Gazebo Classic

[ROS (1) 与 MAVROS 安装指南](../ros/mavros_installation.md) 说明了如何设置 ROS (1)、MAVROS 和 PX4 的工作环境。

::: info
_ROS_ 仅支持 Linux（不支持 macOS 或 Windows）。
:::

## 启动 ROS/模拟器

以下命令可用于启动模拟器并通过 [MAVROS](../ros/mavros_installation.md) 连接 ROS，其中 `fcu_url` 是运行模拟器的计算机的 IP/端口：

```sh
roslaunch mavros px4.launch fcu_url:="udp://:14540@192.168.1.36:14557"
```

要连接到本地主机，请使用此 URL：

```sh
roslaunch mavros px4.launch fcu_url:="udp://:14540@127.0.0.1:14557"
```

::: info
调用 _roslaunch_ 时使用 `-w NUM_WORKERS`（覆盖工作线程数量）和/或 `-v`（详细模式）参数可以显示关于缺失依赖项的警告。例如：

```sh
roslaunch mavros px4.launch fcu_url:="udp://:14540@127.0.0.1:14557"
```

:::

## 使用 ROS 封装器启动 Gazebo Classic

Gazebo Classic 模拟器可以修改以集成直接发布到 ROS 话题的传感器（例如 Gazebo Classic ROS 激光插件）。
为此，Gazebo Classic 必须使用相应的 ROS 封装器启动。

有 ROS 启动脚本可用于运行用 ROS 封装的模拟器：

- [posix_sitl.launch](https://github.com/PX4/PX4-Autopilot/blob/main/launch/posix_sitl.launch): 纯 SITL 启动
- [mavros_posix_sitl.launch](https://github.com/PX4/PX4-Autopilot/blob/main/launch/mavros_posix_sitl.launch): SITL 和 MAVROS

要运行用 ROS 封装的 SITL，需要更新 ROS 环境，然后按常规方式启动：

（可选）：仅在从源代码编译 MAVROS 或其他 ROS 包时才需要源码 catkin 工作空间：

```sh
cd <PX4-Autopilot_clone>
DONT_RUN=1 make px4_sitl_default gazebo-classic
source ~/catkin_ws/devel/setup.bash    # (可选)
source Tools/simulation/gazebo-classic/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)/Tools/simulation/gazebo-classic/sitl_gazebo-classic
roslaunch px4 posix_sitl.launch
```

在自己的启动文件中包含上述提到的其中一个启动文件，以便在模拟器中运行自己的 ROS 应用程序。

## 背后的工作原理

本节展示了前面提供的 _roslaunch_ 指令实际上是如何工作的（您可以按照这些步骤手动启动模拟器和 ROS）。

您需要三个终端，在所有终端中必须源码 ROS 环境。

首先使用以下命令启动模拟器：

```sh
cd <PX4-Autopilot_clone>
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd)
roslaunch px4 px4.launch
```

控制台将显示如下内容：

```sh
INFO  [px4] instance: 0

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4 starting.

INFO  [px4] startup script: /bin/sh etc/init.d-posix/rcS 0
INFO  [init] found model autostart file as SYS_AUTOSTART=10016
INFO  [param] selected parameter default file parameters.bson
INFO  [param] importing from 'parameters.bson'
INFO  [parameters] BSON document size 295 bytes, decoded 295 bytes (INT32:12, FLOAT:3)
INFO  [param] selected parameter backup file parameters_backup.bson
INFO  [dataman] data manager file './dataman' size is 7866640 bytes
etc/init.d-posix/rcS: 31: [: Illegal number:
INFO  [init] starting system components
INFO  [init] initializing parameters
INFO  [init] initializing modules
INFO  [init] starting modules
```

在第二个终端中启动 Gazebo Classic：

```sh
gazebo --verbose worlds/iris.world
```

在第三个终端中启动 MAVROS：

```sh
roslaunch mavros px4.launch
```