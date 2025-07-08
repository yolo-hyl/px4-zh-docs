# 树莓派 - ROS 安装

本指南介绍如何在作为Pixhawk伴飞计算机的Raspberry Pi 2上安装ROS-indigo。

## 前提条件
* 运行正常的Raspberry Pi设备（需连接显示器、键盘或配置好SSH连接）
* 本指南假设您已安装Raspbian "JESSIE"系统。若未安装：[安装系统](https://www.raspberrypi.org/downloads/raspbian/) 或 [升级](http://raspberrypi.stackexchange.com/questions/27858/upgrade-to-raspbian-jessie) Raspbian Wheezy到Jessie

## 安装
请按照[this guide](http://wiki.ros.org/ROSberryPi/Installing%20ROS%20Indigo%20on%20Raspberry%20Pi) 进行ROS Indigo的实际安装。注意：请安装"ROS-Comm"版本。桌面版过于臃肿。

### 安装包时的错误
如果希望下载包（例如 `sudo apt-get install ros-indigo-ros-tutorials`），可能会遇到错误提示："unable to locate package ros-indigo-ros-tutorials"。

若出现此情况，请按以下步骤操作：
进入您的catkin工作空间（例如 ~/ros_catkin_ws）并修改包名。

```sh
$ cd ~/ros_catkin_ws

$ rosinstall_generator ros_tutorials --rosdistro indigo --deps --wet-only --exclude roslisp --tar > indigo-custom_ros.rosinstall
```

接下来使用wstool更新工作空间。

```sh
$ wstool merge -t src indigo-custom_ros.rosinstall

$ wstool update -t src
```

接下来（仍在工作空间文件夹中），加载并编译文件。

```sh
$ source /opt/ros/indigo/setup.bash

$ source devel/setup.bash

$ catkin_make
```