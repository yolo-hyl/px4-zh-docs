# ROS (1) 与 MAVROS 安装指南

:::warning
PX4 开发团队建议所有用户 [升级到 ROS 2](../ros2/index.md)。
本文档反映的是“旧方法”。
:::

本文档介绍如何通过 MAVROS 在 PX4 飞控与启用 ROS 1 的伴飞计算机之间建立通信。

[MAVROS](http://wiki.ros.org/mavros#mavros.2BAC8-Plugins.sys_status) 是一个 ROS 1 包，它为运行 ROS 1 的计算机提供了与任何 MAVLink 启用的飞控、地面站或外设的可扩展通信。  
_MAVROS_ 是 ROS 1 与 MAVLink 协议之间“官方”支持的桥梁。

首先安装 PX4 和 ROS，然后安装 MAVROS。

## 安装 ROS 和 PX4

本节介绍如何安装 [ROS 1](../ros/index.md) 与 PX4。  
ROS 1 完整桌面构建包含 Gazebo Classic，因此通常无需自行安装模拟器依赖！

:::tip
这些说明是 [官方安装指南](https://github.com/mavlink/mavros/tree/master/mavros#installation) 的简化版本。  
它们涵盖 _ROS Melodic 和 Noetic_ 发行版。
:::

:::: tabs

::: tab ROS Noetic (Ubuntu 20.04)

如果在 Ubuntu 20.04 上使用 [ROS Noetic](http://wiki.ros.org/noetic)：

1. 安装不带模拟器工具链的 PX4：

   1. [下载 PX4 源代码](../dev_setup/building_px4.md)：

      ```sh
      git clone https://github.com/PX4/PX4-Autopilot.git --recursive
      ```

   1. 运行 **ubuntu.sh** 并添加 `--no-sim-tools`（可选 `--no-nuttx`）：

      ```sh
      bash ./PX4-Autopilot/Tools/setup/ubuntu.sh --no-sim-tools --no-nuttx
      ```

      - 根据脚本进度确认任何提示。

   1. 完成后重启计算机。

1. 可能需要安装以下附加依赖项：

   ```sh
   sudo apt-get install protobuf-compiler libeigen3-dev libopencv-dev -y
   ```

1. 按照 [Noetic 安装说明](http://wiki.ros.org/noetic/Installation/Ubuntu#Installation) 进行操作（推荐安装 `ros-noetic-desktop-full`）。

:::

::: tab ROS Melodic (Ubuntu 18.04)

如果在 Ubuntu 18.04 上使用 ROS "Melodic：

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
   无需单独安装 MAVROS（如下所示），因为脚本已包含该操作。

   注意：
   - ROS Melodic 默认安装 Gazebo (Classic) 9。
   - 您的 catkin（ROS 构建系统）工作区将创建在 **~/catkin_ws/**。
   - 脚本使用 ROS Wiki "Melodic" [Ubuntu 页面](http://wiki.ros.org/melodic/Installation/Ubuntu) 的说明。
   :::

::::

## 安装 MAVROS

MAVROS 可以从源代码或二进制安装。我们建议开发人员使用源代码安装。

#### 二进制安装（Debian / Ubuntu）

ROS 仓库为 Ubuntu x86、amd64（x86_64）和 armhf（ARMv7）提供了二进制包。  
Kinetic 还支持 Debian Jessie amd64 和 arm64（ARMv8）。

使用 `apt-get` 安装，其中 `${ROS_DISTRO}` 应根据您的 ROS 版本解析为 `kinetic` 或 `noetic`：

```sh
sudo apt-get install ros-${ROS_DISTRO}-mavros ros-${ROS_DISTRO}-mavros-extras ros-${ROS_DISTRO}-mavros-msgs
```

然后通过运行 `install_geographiclib_datasets.sh` 脚本安装 [GeographicLib](https://geographiclib.sourceforge.io/) 数据集：

```sh
wget https://raw.githubusercontent.com/mavlink/mavros/master/mavros/scripts/install_geographiclib_datasets.sh
sudo bash ./install_geographiclib_datasets.sh
```

#### 源代码安装

此安装假设您有一个位于 `~/catkin_ws` 的 catkin 工作区。如果没有，请创建一个：

```sh
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin init
wstool init src
```

您将使用 ROS Python 工具：_wstool_（用于检索源代码）、_rosinstall_ 和 _catkin_tools_（用于构建）。虽然它们可能已在 ROS 安装过程中安装，但您也可以通过以下命令安装：

```sh
sudo apt-get install python-catkin-tools python-rosdep python-wstool
```

:::tip
推荐使用 `catkin build` 而非 `catkin_make` 进行构建，以获得更好的性能和兼容性。
:::

如果尚未初始化工作区，请运行以下命令：

```sh
rosdep init
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro ${ROS_DISTRO} -y
```

然后构建工作区：

```sh
catkin build
```

构建完成后，添加工作区到环境变量：

```sh
source ~/catkin_ws/devel/setup.bash
```

:::warning
如果您在 Ubuntu 18.04 上使用 ROS Melodic，请确保已安装所有依赖项。某些依赖项可能需要从源代码构建。
:::

## MAVROS 示例

以下是使用 MAVROS 的简单示例：

```python
import rospy
from mavros_msgs.msg import State

def state_cb(state):
    rospy.loginfo("Current state: %s", state.mode)

rospy.init_node("mavros_state_printer")
state_sub = rospy.Subscriber("/mavros/state", State, callback=state_cb)
rospy.spin()
```

## 相关链接

- [MAVROS 官方文档](http://wiki.ros.org/mavros)
- [ROS 安装指南](http://wiki.ros.org/Installation)
- [PX4 开发者指南](https://docs.px4.io/)