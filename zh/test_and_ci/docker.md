# PX4 Docker容器

为完整的[PX4开发工具链](../dev_setup/dev_env.md#supported-targets)（包括基于NuttX和Linux的硬件、[Gazebo Classic](../sim_gazebo_classic/index.md)仿真以及[ROS](../simulation/ros_interface.md)）提供了Docker容器。

本主题展示了如何使用[可用的docker容器](#px4_containers)在本地Linux计算机上访问构建环境。

::: info
Dockerfiles和README可在[Github here](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)找到。
它们在[Docker Hub](https://hub.docker.com/u/px4io/)上自动构建。
:::

## 先决条件

::: info
PX4 容器目前仅支持 Linux 系统（如果没有 Linux 系统，可以[在虚拟机中](#virtual_machine)运行容器）
请勿使用 `boot2docker` 和默认 Linux 镜像，因为它没有包含 X-Server。
:::

[安装 Docker](https://docs.docker.com/installation/) 到你的 Linux 电脑，建议使用 Docker 维护的软件包仓库获取最新稳定版本。你可以使用 _Enterprise Edition_ 或（免费的）_Community Edition_。

在 _Ubuntu_ 上进行非生产环境的本地安装时，安装 Docker 最快且最简单的方式是使用 [便捷脚本](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-convenience-script)（如下所示，替代安装方法请参阅同一页面）：

```sh
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

默认安装需要以 root 用户身份调用 _Docker_（即使用 `sudo`）。然而，构建 PX4 固件时，我们建议[以非 root 用户身份使用 docker](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)。这样，使用 docker 后，你的构建文件夹不会被 root 拥有。# 创建docker组（可能不需要）
sudo groupadd docker# 将用户添加到docker组中。
sudo usermod -aG docker $USER# 在使用 Docker 前请再次登录/登出

```

<a id="px4_containers"></a>## 容器层次结构

可用容器在 [Github 此处](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)。

这些容器允许测试各种构建目标和配置（可通过工具名称推断包含的工具）。  
容器是分层的，子容器包含父容器的功能。  
例如，下面的部分层次结构显示：带有 NuttX 构建工具的 Docker 容器（`px4-dev-nuttx-focal`）不包含 ROS 2，而仿真容器包含 ROS 2：

```plain
- px4io/px4-dev-base-focal
  - px4io/px4-dev-nuttx-focal
  - px4io/px4-dev-simulation-focal
    - px4io/px4-dev-ros-noetic
      - px4io/px4-dev-ros2-foxy
  - px4io/px4-dev-ros2-rolling
- px4io/px4-dev-base-jammy
  - px4io/px4-dev-nuttx-jammy
```

最新版本可通过 `latest` 标签访问：`px4io/px4-dev-nuttx-focal:latest`  
（每个容器的可用标签列在 _hub.docker.com_ 上。例如，`px4io/px4-dev-nuttx-focal` 的标签可在此处找到 [here](https://hub.docker.com/r/px4io/px4-dev-nuttx-focal/tags?page=1&ordering=last_updated)）。

:::tip
通常应使用较新的容器，但不一定使用 `latest`（因为其更新过于频繁）。
:::

## 使用 Docker 容器

以下说明展示了如何通过运行在 Docker 容器中的工具链，在宿主计算机上构建 PX4 源代码。
假设您已将 PX4 源代码下载到 **src/PX4-Autopilot** 目录，如下所示：

```sh
mkdir src
cd src
git clone https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot
```

### 辅助脚本 (docker_run.sh)

使用容器的最简单方法是通过辅助脚本 [docker_run.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/docker_run.sh)。
该脚本以 PX4 构建命令作为参数（例如 `make tests`）。它会启动 Docker，使用最新版本（硬编码）的合适容器，并设置合理的环境参数。

例如，在 **/PX4-Autopilot** 目录中构建 SITL 时，可调用：

```sh
./Tools/docker_run.sh 'make px4_sitl_default'
```

或者使用 NuttX 工具链启动 bash 会话：

```sh
./Tools/docker_run.sh 'bash'
```

:::tip
该脚本的优势在于无需了解太多 Docker 知识，也无需考虑使用哪个容器。然而，它并不特别稳定！[下方章节](#manual_start)中讨论的手动方法更加灵活，如果脚本出现问题，建议使用手动方法。
:::

<a id="manual_start"></a>

### 手动调用 Docker

典型命令的语法如下所示。
此命令运行一个支持 X 转发的 Docker 容器（使仿真图形界面可在容器内部访问）。
它将宿主机上的 `<host_src>` 目录映射到容器内的 `<container_src>` 目录，并转发连接 QGroundControl 所需的 UDP 端口。
使用 `-–privileged` 选项后，容器会自动访问宿主机上的设备（例如摇杆和 GPU）。如果连接/断开设备，需要重启容器。

```sh

```# 启用容器对xhost的访问权限

xhost +# 运行 Docker

```sh
docker run -it --privileged \
    --env=LOCAL_USER_ID="$(id -u)" \
    -v <host_src>:<container_src>:rw \
    -v /tmp/.X11-unix:/tmp/.X11-unix:ro \
    -e DISPLAY=:0 \
    -p 14570:14570/udp \
    --name=<local_container_name> <container>:<tag> <build_command>
```

其中，

- `<host_src>`: 主机上的目录，映射到容器内的`<container_src>`。通常应为 **PX4-Autopilot** 目录。
- `<container_src>`: 容器内共享（源）目录的位置。
- `<local_container_name>`: 创建的 Docker 容器名称。如需后续引用该容器可使用此名称。
- `<container>:<tag>`: 要启动的容器及版本标签，例如：`px4io/px4-dev-ros:2017-10-23`。
- `<build_command>`: 在新容器中执行的命令。例如使用 `bash` 可在容器内打开 bash 终端。

以下具体示例展示如何打开 bash 终端并共享主机上的 **~/src/PX4-Autopilot** 目录。

```sh
```# 从容器启用对xhost的访问
xhost +# 运行 Docker 并打开 Bash Shell

```sh
docker run -it --privileged \
--env=LOCAL_USER_ID="$(id -u)" \
-v ~/src/PX4-Autopilot:/src/PX4-Autopilot/:rw \
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
-e DISPLAY=:0 \
--network host \
--name=px4-ros px4io/px4-dev-ros2-foxy:2022-07-31 bash
```

::: info
我们使用 host 网络模式以避免当 QGroundControl 与 Docker 容器在相同系统上运行时，UDP 端口访问控制的冲突。
:::

::: info
如果遇到错误 "Can't open display: :0"，可能需要将 DISPLAY 设置为其他值。
在 Linux (XWindow) 主机上，可以将 `-e DISPLAY=:0` 替换为 `-e DISPLAY=$DISPLAY`。
在其他主机上，可以尝试修改 `-e DISPLAY=:0` 中的 `0` 值，直到 "Can't open display: :0" 错误消失。
:::

如果一切顺利，你现在应该进入了一个新的 Bash Shell。
可以通过运行 SITL 来验证是否一切正常：

```sh
cd src/PX4-Autopilot    #This is <container_src>
make px4_sitl_default gazebo-classic
```

### 重新进入容器

`docker run` 命令只能用于创建新容器。要重新进入这个容器（包含你之前的修改），只需执行：# 启动容器
docker start container_name# 在容器中打开一个新的bash shell
docker exec -it container_name bash

```

如果需要多个连接到容器的shell，只需打开一个新的shell并再次执行最后一条命令。

### 清除容器

有时您可能需要完全清除容器。可以使用容器名称执行此操作：

```sh
docker rm mycontainer
```

如果您记不起名称，则可以列出非活动容器ID然后删除它们，如下所示：

```sh
docker ps -a -q
45eeb98f1dd9
docker rm 45eeb98f1dd9
```

### QGroundControl

当在docker容器内运行模拟实例（例如SITL）并通过主机上的_QGroundControl_进行控制时，需要手动设置通信链路。_QGroundControl_的自动连接功能在此场景下不可用。

在_QGroundControl_中，导航到[Settings](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html)并选择Comm Links。创建一个使用UDP协议的新链接。端口号取决于使用的[配置](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)，例如SITL配置使用14570端口。IP地址是您的docker容器地址，通常默认网络下为172.17.0.1/16。可以通过以下命令获取docker容器的IP地址（假设容器名称为`mycontainer`）：

```sh
$ docker inspect -f '{ {range .NetworkSettings.Networks}}{ {.IPAddress}}{ {end}}' mycontainer
```

::: info
双花括号之间的空格不应存在（需要它们来避免gitbook的UI渲染问题）。
:::

### 故障排除

#### 权限错误

容器会以默认用户（通常为"root"）创建文件。这可能导致权限错误，主机用户无法访问容器创建的文件。

上面的示例使用了`--env=LOCAL_USER_ID="$(id -u)"`行，在容器中创建与主机用户相同UID的用户。这确保了容器内创建的所有文件在主机上均可访问。

#### 图形驱动问题

运行Gazebo Classic时可能会出现类似以下错误：

```sh
libGL error: failed to load driver: swrast
```

此时需要为您的主机系统安装原生图形驱动。下载正确的驱动并在容器中安装。对于Nvidia驱动，应使用以下命令（否则安装程序会检测到主机加载的模块并拒绝继续）：

```sh
./NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module
```

更多信息请见[这里](http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/)。

<a id="virtual_machine"></a>## 虚拟机支持

任何近期的Linux发行版都应能正常工作。

以下配置经过测试验证：

- OS X 系统使用 VMWare Fusion 和 Ubuntu 14.04（Docker容器在Parallels上启用GUI支持会导致X-Server崩溃）

**内存**

虚拟机建议至少分配4GB内存。

**编译问题**

如果编译失败并出现类似以下错误：

```sh
The bug is not reproducible, so it is likely a hardware or OS problem.
c++: internal compiler error: Killed (program cc1plus)
```

尝试禁用并行编译。

**允许从虚拟机主机控制Docker**

编辑 `/etc/defaults/docker` 文件并添加以下行：

```sh
DOCKER_OPTS="${DOCKER_OPTS} -H unix:///var/run/docker.sock -H 0.0.0.0:2375"
```

随后可以从宿主操作系统控制docker：

```sh
export DOCKER_HOST=tcp://<ip of your VM>:2375

```# 运行一些 Docker 命令以测试其是否正常工作，例如 ps
docker ps`