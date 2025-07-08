# PX4 ROS 2接口库

<Badge type="tip" text="PX4 v1.15" /> <Badge type="warning" text="实验性" />

:::warning 实验性
撰写本文时，PX4 ROS 2接口库的部分功能仍处于实验阶段，因此可能会发生变更。
:::

[PX4 ROS 2接口库](https://github.com/Auterion/px4-ros2-interface-lib) 是一个C++库，可简化从ROS 2控制和与PX4交互的过程。

该库为开发者提供两种高级接口：

1. [控制接口](./px4_ros2_control_interface.md) 允许开发者创建并动态注册使用ROS 2编写的模式。  
   它提供了发送不同类型设定值的类，范围从高层导航任务到直接执行器控制。
2. [导航接口](./px4_ros2_navigation_interface.md) 能够从ROS 2应用（例如VIO系统）向PX4发送机体位置估计值。

<!--
## 概述
-->

## 在ROS 2工作空间中的安装

在现有ROS 2工作空间中使用该库的步骤如下：

1. 确保已配置有效的[ROS 2环境](../ros2/user_guide.md)，并在ROS 2工作空间中包含 [`px4_msgs`](https://github.com/PX4/px4_msgs)。
2. 将仓库克隆到工作空间：

   ```sh
   cd $ros_workspace/src
   git clone --recursive https://github.com/Auterion/px4-ros2-interface-lib
   ```

   ::: info
   为确保兼容性，请为PX4、_px4_msgs_ 和库使用最新的_main_分支。  
   参见[此处](https://github.com/Auterion/px4-ros2-interface-lib#compatibility-with-px4)。
   :::

3. 构建工作空间：

   ```sh
   cd ..
   colcon build
   source install/setup.bash
   ```

<!--
## 如何使用该库
-->

## CI：集成测试

向PX4提交拉取请求时，CI会运行库的集成测试。  
这些测试验证模式注册、故障保护和模式替换是否按预期工作。

更多详情请参见 [PX4 ROS2接口库集成测试](../test_and_ci/integration_testing_px4_ros2_interface.md)。