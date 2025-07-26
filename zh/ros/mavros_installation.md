

# ROS 1与MAVROS安装指南

:::warning
PX4开发团队建议所有用户[升级到ROS 2](../ros2/index.md)。
本文档反映的是"旧方法"。
:::

本文档说明如何通过MAVROS在PX4飞控和ROS 1伴飞计算机之间建立通信。

[MAVROS](http://wiki.ros.org/mavros#mavros.2BAC8-Plugins.sys_status) 是一个ROS 1软件包，它允许运行ROS 1的计算机与任何支持MAVLink的飞控、地面站或外设进行MAVLink可扩展通信。
_MAVROS_ 是ROS 1与MAVLink协议之间官方支持的桥梁。

首先安装PX4和ROS，然后安装MAVROS。

## 安装 ROS 和 PX4

本节说明如何安装 [ROS 1](../ros/index.md) 与 PX4。  
ROS 1 的完整桌面安装包包含 Gazebo Classic，因此通常无需自行安装模拟器依赖项！

:::tip  
这些说明是 [官方安装指南](https://github.com/mavlink/mavros/tree/master/mavros#installation) 的简化版本。  
它们涵盖 _ROS Melodic 和 Noetic_ 发行版。  
:::

:::: tabs

::: tab ROS Noetic (Ubuntu 20.04)

如果您在 Ubuntu 20.04 上使用 [ROS Noetic](http://wiki.ros.org/noetic)：

1. 以不包含模拟器工具链的方式安装 PX4：

   1. [下载 PX4 源代码](../dev_setup/building_px4.md)：

      ```sh
      git clone https://github.com/PX4/PX4-Autopilot.git --recursive
      ```

   1. 运行 **ubuntu.sh** 脚本并添加 `--no-sim-tools`（以及可选的 `--no-nuttx`）参数：

      ```sh
      bash ./PX4-Autopilot/Tools/setup/ubuntu.sh --no-sim-tools --no-nuttx
      ```

      - 在脚本执行过程中确认任何提示。

   1. 完成后重启计算机。

1. 您 _可能_ 需要安装以下附加依赖项：

   ```sh
   sudo apt-get install protobuf-compiler libeigen3-dev libopencv-dev -y
   ```

1. 跟随 [Noetic 安装指南](http://wiki.ros.org/noetic/Installation/Ubuntu#Installation)（推荐安装 ros-noetic-desktop-full）。

:::

::: tab ROS Melodic (Ubuntu 18.04)

如果您在 Ubuntu 18.04 上使用 ROS "Melodic"：

1. 在 bash shell 中下载 [ubuntu_sim_ros_melodic.sh](https://raw.githubusercontent.com/PX4/Devguide/master/build_scripts/ubuntu_sim_ros_melodic.sh) 脚本：

   ```sh
   wget https://raw.githubusercontent.com/PX4/Devguide/master/build_scripts/ubuntu_sim_ros_melodic.sh
   ```

1. 运行脚本：

   ```sh
   bash ubuntu_sim_ros_melodic.sh
   ```

   脚本执行过程中可能需要确认一些提示。

   ::: tip  
   您无需安装 MAVROS（如下所示），因为脚本已包含该依赖。

   同时注意：  
   - ROS Melodic 默认安装 Gazebo (Classic) 9。  
   - 您的 catkin（ROS 构建系统）工作区将创建在 **~/catkin_ws/**。  
   - 脚本使用来自 ROS Wiki "Melodic" [Ubuntu 页面](http://wiki.ros.org/melodic/Installation/Ubuntu) 的安装说明。  
   :::

::::

## 安装 MAVROS

然后MAVROS可以从源码或二进制安装。
我们建议开发者使用源码安装。

#### 二进制安装（Debian / Ubuntu）

ROS 仓库为 Ubuntu x86、amd64（x86_64）和 armhf（ARMv7）提供了二进制包。  
Kinetic 同时支持 Debian Jessie 的 amd64 和 arm64（ARMv8）。

使用 `apt-get` 安装，其中下方的 `${ROS_DISTRO}` 应根据您的 ROS 版本解析为 `kinetic` 或 `noetic`：

```sh
sudo apt-get install ros-${ROS_DISTRO}-mavros ros-${ROS_DISTRO}-mavros-extras ros-${ROS_DISTRO}-mavros-msgs
```

然后通过运行 `install_geographiclib_datasets.sh` 脚本来安装 [GeographicLib](https://geographiclib.sourceforge.io/) 数据集：

```sh
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
sudo bash ./install_geographiclib_datasets.sh
```

#### 源码安装

此安装方式假设你有一个位于 `~/catkin_ws` 的 catkin 工作空间。如果没有，可以通过以下命令创建：

```sh
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin init
wstool init src
```

你将使用 ROS Python 工具：_wstool_（用于获取源码）、_rosinstall_ 和 _catkin_tools_（用于构建）。虽然这些工具可能已包含在你的 ROS 安装中，但你也可以通过以下命令安装：

```sh
sudo apt-get install python-catkin-tools python-rosinstall-generator -y
```

:::tip
虽然可以使用 **catkin_make** 构建该软件包，但推荐使用 **catkin_tools**，因为它是一个更强大且友好的构建工具。
:::

如果你是首次使用 wstool，需要通过以下命令初始化源码空间：

```sh
$ wstool init ~/catkin_ws/src
```

现在你可以开始构建：

1. 安装 MAVLink：

   ```sh
   # 我们为所有 ROS 发行版使用 Kinetic 的参考版本，因为它不依赖具体发行版且更新及时
   rosinstall_generator --rosdistro kinetic mavlink | tee /tmp/mavros.rosinstall
   ```

1. 通过源码安装 MAVROS，可以选择稳定版本或最新版本：

   - 稳定版本

     ```sh
     rosinstall_generator --upstream mavros | tee -a /tmp/mavros.rosinstall
     ```

   - 最新源码

     ```sh
     rosinstall_generator --upstream-development mavros | tee -a /tmp/mavros.rosinstall
     ```

     ```sh
     # 为了将所有依赖项添加到你的 catkin_ws 中，
     # 只需在上述命令中添加 '--deps'，例如：
     #   rosinstall_generator --upstream mavros --deps | tee -a /tmp/mavros.rosinstall
     ```

1. 创建工作空间并安装依赖项：

   ```sh
   wstool merge -t src /tmp/mavros.rosinstall
   wstool update -t src -j4
   rosdep install --from-paths src --ignore-src -y
   ```

1. 安装 [GeographicLib](https://geographiclib.sourceforge.io/) 数据集：

   ```sh
   ./src/mavros/mavros/scripts/install_geographiclib_datasets.sh
   ```

1. 构建源码：

   ```sh
   catkin build
   ```

1. 确保使用工作空间中的 setup.bash 或 setup.zsh：

   ```sh
   #必须的，否则 rosrun 无法找到该工作空间的节点
   source devel/setup.bash
   ```

如果出现错误，可以参阅 [mavros 仓库](https://github.com/mavlink/mavros/tree/master/mavros#installation) 中的附加安装和故障排除说明。

## MAVROS 示例

[MAVROS Offboard 示例 (C++)](../ros/mavros_offboard_cpp.md) 将展示 MAVROS 的基础用法，包括读取遥测数据、检查无人机状态、切换飞行模式以及控制无人机。

::: info
如果你有使用 PX4 Autopilot 和 MAVROS 的示例应用，我们可以帮助你将其添加到我们的文档中。
:::

## 参见

- [mavros ROS 包摘要](http://wiki.ros.org/mavros#mavros.2BAC8-Plugins.sys_status)
- [mavros 源码](https://github.com/mavlink/mavros/)
- [ROS Melodic 安装说明](http://wiki.ros.org/melodic/Installation)