

# PX4 Docker容器

Docker容器支持完整的[PX4开发工具链](../dev_setup/dev_env.md#supported-targets)（包括NuttX和基于Linux的硬件）、[Gazebo Classic](../sim_gazebo_classic/index.md)仿真以及[ROS](../simulation/ros_interface.md)。

本主题展示如何通过[可用的docker容器](#px4_containers)在本地Linux计算机上访问构建环境。

::: info
Dockerfiles和README可在[Github此处](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)找到。
它们在[Docker Hub](https://hub.docker.com/u/px4io/)上自动构建。
:::

## 先决条件

::: info
PX4 容器目前仅支持 Linux 系统（如果没有 Linux 系统，可以[在虚拟机中运行容器](#virtual_machine)）。
不要使用 `boot2docker` 与默认 Linux 镜像，因为它不包含 X-Server。
:::

[安装 Docker](https://docs.docker.com/installation/) 到你的 Linux 计算机上，建议使用 Docker 维护的软件包仓库以获取最新稳定版本。你可以选择使用 _Enterprise Edition_ 或（免费的）_Community Edition_。

对于 _Ubuntu_ 上的非生产环境本地安装，最快捷的 Docker 安装方式是使用 [便捷脚本](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-convenience-script)（如下所示，其他安装方法也可在同一页找到）：

```sh
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

默认安装需要以 root 用户身份调用 _Docker_（即使用 `sudo`）。然而，对于 PX4 固件的构建，我们建议 [以非 root 用户身份使用 docker](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)。这样，使用 docker 后，你的构建文件夹不会归 root 用户所有。

# 创建 Docker 用户组（可能不需要）
sudo groupadd docker

# 将您的用户添加到 docker 组中。
sudo usermod -aG docker $USER

# 在使用docker前请重新登录/登出
```

## 容器层级

可用的容器在 [Github此处](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)。

这些容器允许测试各种构建目标和配置（可通过其名称推断包含的工具）。
容器是层级结构的，子容器继承父容器的功能。
例如，下面的部分层级显示了`px4-dev-nuttx-focal`容器不包含ROS2，而模拟容器包含：

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

最新版本可以通过`latest`标签访问：`px4io/px4-dev-nuttx-focal:latest`
（每个容器的可用标签在_hub.docker.com_上列出。
例如，`px4io/px4-dev-nuttx-focal`的标签可在此处找到[here](https://hub.docker.com/r/px4io/px4-dev-nuttx-focal/tags?page=1&ordering=last_updated))。

:::tip
通常你应该使用较新的容器，但不一定是`latest`（因为这会频繁变更）。
:::

## 使用 Docker 容器

以下说明展示了如何通过运行在 Docker 容器中的工具链，在主机上构建 PX4 源代码。  
假设您已将 PX4 源代码下载到 **src/PX4-Autopilot**，如下所示：

```sh
mkdir src
cd src
git clone https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot
```

### 辅助脚本 (docker_run.sh)

使用容器最简单的方式是通过 [docker_run.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/docker_run.sh) 辅助脚本。  
该脚本接受一个 PX4 构建命令作为参数（例如 `make tests`）。它会启动 Docker，并使用最新版本（硬编码）的适当容器和合理的环境设置。

例如，要构建 SITL，可以调用以下命令（需在 **/PX4-Autopilot** 目录中执行）：

```sh
./Tools/docker_run.sh 'make px4_sitl_default'
```

或者使用 NuttX 工具链启动一个 bash 会话：

```sh
./Tools/docker_run.sh 'bash'
```

:::tip
该脚本的优势在于无需了解太多关于 _Docker_ 的知识，也不需要思考使用哪个容器。然而它并不特别稳定！下面将讨论的[手动启动方式](#manual_start)更为灵活，如果脚本出现任何问题，建议使用该方式。
:::

<a id="manual_start"></a>

### 手动调用 Docker

典型命令的语法如下所示。
此命令运行一个支持 X 转发的 Docker 容器（使仿真图形界面可在容器内部使用）。
它将你的计算机上的目录 `<host_src>` 映射到容器内的 `<container_src>`，并转发连接 _QGroundControl_ 所需的 UDP 端口。
通过 `-–privileged` 选项，它将自动访问主机上的设备（例如操纵杆和 GPU）。如果连接/断开设备，必须重新启动容器。

```sh

```

# 启用容器对xhost的访问
xhost +

# 运行 docker  
docker run -it --privileged \  
    --env=LOCAL_USER_ID="$(id -u)" \  
    -v <host_src>:<container_src>:rw \  
    -v /tmp/.X11-unix:/tmp/.X11-unix:ro \  
    -e DISPLAY=:0 \  
    -p 14570:14570/udp \  
    --name=<local_container_name> <container>:<tag> <build_command>  
```

其中，  

- `<host_src>`: 映射到容器中`<container_src>`的主机目录。通常应为 **PX4-Autopilot** 目录。  
- `<container_src>`: 容器内部共享（源代码）目录的位置。  
- `<local_container_name>`: 要创建的 docker 容器的名称。如果需要再次引用该容器，可使用此名称。  
- `<container>:<tag>`: 要启动的容器及版本标签。例如：`px4io/px4-dev-ros:2017-10-23`。  
- `<build_command>`: 在新建容器中调用的命令。例如使用 `bash` 可在容器中打开 bash 终端。  

下面的具体示例展示如何打开 bash 终端并共享主机上 **~/src/PX4-Autopilot** 目录。  

```sh

# 启用容器对xhost的访问
xhost +

# 运行 docker 并打开 bash shell
docker run -it --privileged \
--env=LOCAL_USER_ID="$(id -u)" \
-v ~/src/PX4-Autopilot:/src/PX4-Autopilot/:rw \
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
-e DISPLAY=:0 \
--network host \
--name=px4-ros px4io/px4-dev-ros2-foxy:2022-07-31 bash
```

::: info
我们使用主机网络模式以避免当在同一系统上使用 QGroundControl 时，UDP 端口访问控制产生的冲突。
:::

::: info
如果遇到错误 "Can't open display: :0"，可能需要将 `DISPLAY` 设置为其他值。
在 Linux (XWindow) 主机上可以将 `-e DISPLAY=:0` 改为 `-e DISPLAY=$DISPLAY`。
在其他主机上可能需要尝试修改 `-e DISPLAY=:0` 中的 `0` 值，直到 "Can't open display: :0" 错误消失。
:::

如果一切正常，你现在应该进入了一个新的 bash shell。
通过运行例如 SITL 来验证是否正常工作：

```sh
cd src/PX4-Autopilot    #This is <container_src>
make px4_sitl_default gazebo-classic
```

### 重新进入容器

`docker run` 命令仅可用于创建新容器。要重新进入该容器（其中将保留你的更改），只需执行：  

```sh
```

# 启动容器

docker start container_name

# 在容器中打开一个新的 bash shell  
`docker exec -it container_name bash`  

如果您需要多个连接到容器的 shell，只需打开一个新的 shell 并再次执行上一条命令。

### 清除容器

有时你可能需要完全清除一个容器。你可以通过其名称来执行此操作：

```sh
docker rm mycontainer
```

如果你不记得名称，可以列出非活动容器的ID，然后删除它们，如下所示：

```sh
docker ps -a -q
45eeb98f1dd9
docker rm 45eeb98f1dd9
```

### QGroundControl

当在 docker 容器中运行模拟实例（例如 SITL）并通过 _QGroundControl_ 从主机控制时，需要手动设置通信链路。_QGroundControl_ 的自动连接功能在此场景下无法使用。

在 _QGroundControl_ 中，导航到 [设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html) 并选择通信链路。创建一个使用 UDP 协议的新链接。端口号取决于所使用的 [配置](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)（例如 SITL 配置使用端口 14570）。IP 地址是你的 docker 容器地址，通常在使用默认网络时为 172.17.0.1/16。可通过以下命令获取 docker 容器的 IP 地址（假设容器名称为 `mycontainer`）：

```sh
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mycontainer
```

::: info
上述双花括号内的空格不应存在（添加这些空格是为了避免 gitbook 的 UI 渲染问题）
:::

### 故障排查

#### 权限错误

容器会按需以默认用户（通常为 "root"）创建文件，这可能导致权限错误，当宿主机用户无法访问容器创建的文件时。

上述示例中使用了 `--env=LOCAL_USER_ID="$(id -u)"` 这行代码，在容器中创建与宿主机用户具有相同 UID 的用户。这可确保容器内创建的所有文件都能在宿主机上访问。

#### 图形驱动问题

运行 Gazebo Classic 时可能会出现类似以下的错误信息：

```sh
libGL error: failed to load driver: swrast
```

在这种情况下，必须为您的宿主系统安装原生图形驱动程序。下载正确的驱动并将其安装在容器内。对于 Nvidia 驱动，应使用以下命令（否则安装程序会检测到宿主系统加载的模块并拒绝继续）：

```sh
./NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module
```

有关此问题的更多信息，请参考[此处](http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/)。

<a id="virtual_machine"></a>

## 虚拟机支持

任何近期的Linux发行版都应该可以正常工作。

以下配置已通过测试：

- OS X 搭配 VMWare Fusion 和 Ubuntu 14.04（Docker容器在Parallels上启用了GUI支持会导致X-Server崩溃）

**内存**

虚拟机至少使用4GB内存。

**编译问题**

如果编译失败并出现类似以下错误：

```sh
The bug is not reproducible, so it is likely a hardware or OS problem.
c++: internal compiler error: Killed (program cc1plus)
```

尝试禁用并行构建。

**允许从虚拟机宿主机控制Docker**

编辑 `/etc/defaults/docker` 并添加以下行：

```sh
DOCKER_OPTS="${DOCKER_OPTS} -H unix:///var/run/docker.sock -H 0.0.0.0:2375"
```

然后可以从宿主操作系统控制docker：

```sh
export DOCKER_HOST=tcp://<ip of your VM>:2375

```

# 运行一些 docker 命令以查看是否正常工作，例如 ps
docker ps