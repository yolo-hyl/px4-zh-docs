

# PX4 Docker容器

Docker容器提供完整的[PX4开发工具链](../dev_setup/dev_env.md#supported-targets)支持，包括基于NuttX和Linux的硬件、[Gazebo Classic](../sim_gazebo_classic/index.md)仿真以及[ROS](../simulation/ros_interface.md)。

本主题展示如何使用[可用的docker容器](#px4_containers)在本地Linux计算机中访问构建环境。

::: info
Dockerfile和README文件可在[Github此处](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)找到。
它们会在[Docker Hub](https://hub.docker.com/u/px4io/)上自动构建。
:::

## 前提条件

::: info
PX4容器目前仅支持Linux（如果没有Linux，可以在[虚拟机](#virtual_machine)中运行容器）。
不要将`boot2docker`与默认Linux镜像一起使用，因为其中不包含X-Server。
:::

[安装Docker](https://docs.docker.com/installation/) 到您的Linux计算机，建议使用Docker维护的软件包仓库以获取最新稳定版本。您可以使用_Enterprise Edition_ 或（免费的）_Community Edition_。

对于在_Ubuntu_上安装非生产环境的本地设置，安装Docker最快且最简单的方法是使用[便捷脚本](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-convenience-script)，如下所示（其他安装方法可在同一页找到）：

```sh
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

默认安装需要您以root用户身份调用_Docker_（即使用`sudo`）。然而，为了构建PX4固件，我们建议[以非root用户身份使用docker](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)。这样，使用docker后，您的构建文件夹不会由root拥有。

# 创建 docker 组（可能不需要）
sudo groupadd docker

# 将您的用户添加到docker组中。  
sudo usermod -aG docker $USER

# 在使用 Docker 之前请重新登录/登出！

```

<a id="px4_containers"></a>

## 容器层级

可用容器在 [在此](https://github.com/PX4/PX4-containers/tree/master?tab=readme-ov-file#container-hierarchy)。

这些容器允许测试各种构建目标和配置（可通过工具名称推断包含的工具）。  
容器是层级结构的，子容器包含其父容器的功能。  
例如，下图的部分层级结构显示，带有 NuttX 构建工具的 Docker 容器（`px4-dev-nuttx-focal`）不包含 ROS 2，而仿真容器则包含：

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

最新版本可以通过 `latest` 标签访问：`px4io/px4-dev-nuttx-focal:latest`  
（每个容器的可用标签列表见 _hub.docker.com_。例如，`px4io/px4-dev-nuttx-focal` 的标签列表可在此处找到 [此处](https://hub.docker.com/r/px4io/px4-dev-nuttx-focal/tags?page=1&ordering=last_updated))。

:::tip
通常建议使用较新的容器，但不一定非要是 `latest`（因为该标签更新过于频繁）。
:::

## 使用 Docker 容器

以下说明展示如何使用运行在 Docker 容器中的工具链，在主机上构建 PX4 源代码。  
信息假设您已将 PX4 源代码下载到 **src/PX4-Autopilot**，如下所示：

```sh
mkdir src
cd src
git clone https://github.com/PX4/PX4-Autopilot.git
cd PX4-Autopilot
```

### 辅助脚本（docker_run.sh）

使用容器最简便的方式是通过[辅助脚本 docker_run.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/docker_run.sh)。  
该脚本接受一个 PX4 构建命令作为参数（例如 `make tests`）。它会启动 Docker，并使用适当容器的最新版本（硬编码）及合理的环境设置。

例如，要构建 SITL，您需要在 **/PX4-Autopilot** 目录内调用：

```sh
./Tools/docker_run.sh 'make px4_sitl_default'
```

或者使用 NuttX 工具链启动 bash 会话：

```sh
./Tools/docker_run.sh 'bash'
```

:::tip
该脚本简单易用，因为您无需了解太多关于 Docker 的知识，也不用考虑使用哪个容器。然而它并不是特别稳定！[下方章节](#manual_start)中讨论的手动方法更为灵活，如果脚本遇到问题，建议使用手动方法。
:::

<a id="manual_start"></a>

### 手动调用 Docker

典型命令的语法如下所示。  
这会运行一个支持 X 转发的 Docker 容器（使模拟器图形界面在容器内可用）。  
它将计算机上的目录 `<host_src>` 映射到容器内的 `<container_src>`，并转发连接 _QGroundControl_ 所需的 UDP 端口。  
通过 `-–privileged` 选项，容器将自动获得对主机设备的访问权限（例如操纵杆和 GPU）。如果插入/拔出设备，必须重新启动容器。

```sh
```

# 从容器启用对xhost的访问
xhost +

# 运行Docker

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

- `<host_src>`：主机上需要映射到容器中`<container_src>`的目录。通常应为 **PX4-Autopilot** 目录。
- `<container_src>`：容器内部共享（源代码）目录的位置。
- `<local_container_name>`：要创建的Docker容器的名称。后续需要引用该容器时可使用此名称。
- `<container>:<tag>`：要启动的容器及版本标签 - 例如：`px4io/px4-dev-ros:2017-10-23`。
- `<build_command>`：在新容器中执行的命令。例如使用`bash`可在容器中打开bash终端。

下文具体示例展示如何在容器中打开bash终端并共享主机上的目录 **~/src/PX4-Autopilot**。

```sh
```

# 启用容器对xhost的访问权限
xhost +

# 运行 Docker 并打开 bash shell
docker run -it --privileged \
--env=LOCAL_USER_ID="$(id -u)" \
-v ~/src/PX4-Autopilot:/src/PX4-Autopilot/:rw \
-v /tmp/.X11-unix:/tmp/.X11-unix:ro \
-e DISPLAY=:0 \
--network host \
--name=px4-ros px4io/px4-dev-ros2-foxy:2022-07-31 bash
```

::: info
我们使用主机网络模式，以避免在相同系统上使用 QGroundControl 和 Docker 容器时因 UDP 端口访问控制引发的冲突。
:::

::: info
如果遇到错误 "Can't open display: :0"，可能需要将 DISPLAY 设置为其他值。
在 Linux（XWindow）主机上可以将 `-e DISPLAY=:0` 替换为 `-e DISPLAY=$DISPLAY`。
在其他主机上可以尝试修改 `-e DISPLAY=:0` 中的 `0` 直到 "Can't open display: :0" 错误消失。
:::

如果一切顺利，你现在应该进入了一个新的 bash shell。
通过运行例如 SITL 来验证是否正常工作：

```sh
cd src/PX4-Autopilot    #This is <container_src>
make px4_sitl_default gazebo-classic
```

### 重新进入容器

`docker run` 命令只能用于创建新容器。要重新进入此容器（其中会保留你的更改），只需执行：

```sh
```

# 启动容器

docker start container_name

# 在此容器中打开一个新的 bash shell  
`docker exec -it container_name bash`  

如果你需要多个连接到该容器的 shell，只需打开一个新的 shell 并再次执行上一条命令即可。

### 清除容器

有时你可能需要完全清除一个容器。你可以通过其名称来执行此操作：

```sh
docker rm mycontainer
```

如果你不记得名称，可以通过列出非活跃容器的ID然后删除它们，如下所示：

```sh
docker ps -a -q
45eeb98f1dd9
docker rm 45eeb98f1dd9
```

### QGroundControl

当在docker容器中运行仿真实例（例如SITL）并通过主机上的_QGroundControl_进行控制时，需要手动设置通信链接。_QGroundControl_的自动连接功能在此场景下无法工作。

在_QGroundControl_中，导航到[设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html)并选择通信链接。创建一个使用UDP协议的新链接。端口号取决于所用的[配置](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/rcS)，例如SITL配置使用端口14570。IP地址是你的docker容器地址，通常默认网络下为172.17.0.1/16。可通过以下命令查找docker容器的IP地址（假设容器名称为`mycontainer`）：

```sh
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' mycontainer
```

::: info
上述双花括号之间的空格不应存在（它们用于避免gitbook中的UI渲染问题）。
:::

### 故障排除

#### 权限错误

容器会以默认用户（通常为"root"）身份按需创建文件。这可能导致权限错误，当主机上的用户无法访问容器创建的文件时就会发生这种情况。

上述示例使用了 `--env=LOCAL_USER_ID="$(id -u)"` 这一行代码，在容器中创建与主机用户具有相同UID的用户。这确保了容器内创建的所有文件都能在主机上被访问。

#### 图形驱动问题

运行Gazebo Classic可能导致类似以下的错误信息：

```sh
libGL error: failed to load driver: swrast
```

在这种情况下，必须为宿主系统安装原生图形驱动程序。下载合适的驱动程序并在容器内安装。对于Nvidia驱动程序应使用以下命令（否则安装程序会检测到宿主已加载的模块并拒绝继续）：

```sh
./NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module
```

更多信息请参考[此处](http://gernotklingler.com/blog/howto-get-hardware-accelerated-opengl-support-docker/)。

<a id="virtual_machine"></a>

## 虚拟机支持

任何较新的Linux发行版均可正常工作。

以下配置经过测试：

- OS X 搭配 VMWare Fusion 和 Ubuntu 14.04（在 Parallels 上带有 GUI 支持的 Docker 容器会导致 X 服务器崩溃）。

**内存**

至少为虚拟机分配4GB内存。

**编译问题**

如果编译失败并出现如下错误：

```sh
The bug is not reproducible, so it is likely a hardware or OS problem.
c++: internal compiler error: Killed (program cc1plus)
```

尝试禁用并行构建。

**允许从虚拟机宿主控制 Docker**

编辑 `/etc/defaults/docker` 并添加以下行：

```sh
DOCKER_OPTS="${DOCKER_OPTS} -H unix:///var/run/docker.sock -H 0.0.0.0:2375"
```

然后您可以从宿主操作系统控制 Docker：

```sh
export DOCKER_HOST=tcp://<ip of your VM>:2375

```

# 运行某个 docker 命令以验证其是否正常工作，例如 ps
docker ps
```