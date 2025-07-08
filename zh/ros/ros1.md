# ROS 1（已弃用）

::: warning
PX4 开发团队建议用户迁移至 [ROS 2](../ros2/index.md)（即跳过本节）！

ROS 1 现在被标记为"discommended"，因为最后一个 LTS 版本即将到达生命周期终点。
ROS 2 与 PX4 的集成深度更高，可实现更低延迟的通信并访问 PX4 内部消息。

:::

[ROS (1)](http://www.ros.org/) 是一个通用机器人库，可用于 PX4 的无人机应用开发。

此版本 ROS 使用 [MAVROS](../ros/mavros_installation.md) 包通过 [MAVLink](../middleware/mavlink.md) 与 PX4 通信（MAVROS 将 ROS 话题桥接到 MAVLink 和 PX4 协议）。

## 话题

- [ROS/MAVROS 安装指南](../ros/mavros_installation.md)：使用 ROS 1 和 MAVROS 配置 PX4 开发环境。
- [ROS/MAVROS 机载示例（C++）](../ros/mavros_offboard_cpp.md)：演示编写 C++ MAVROS/ROS 节点核心概念的教程。
- [ROS MAVROS 发送自定义消息](../ros/mavros_custom_messages.md)
- [ROS 与 Gazebo Classic 仿真](../simulation/ros_interface.md)
- [Gazebo Classic OctoMap 模型与 ROS](../sim_gazebo_classic/octomap.md)
- [RPi 上的 ROS 安装](../ros/raspberrypi_installation.md)
- [外部位置估计（视觉/运动基）](../ros/external_position_estimation.md)

## 更多信息

- [XTDrone](https://github.com/robin-shaun/XTDrone/blob/master/README.en.md) - 用于计算机视觉的 ROS + PX4 仿真环境。
  [XTDrone 手册](https://www.yuque.com/xtdrone/manual_en) 包含您开始所需的所有内容！
- [Prometheus 自主无人机项目](https://github.com/amov-lab/Prometheus/blob/master/README_EN.md) - Prometheus 是基于 ROS 1 的 BSD-3 许可自主无人机软件包集合，由 [AMOVLab](https://github.com/amov-lab) 提供，为无人机智能自主飞行提供完整解决方案（如地图构建、定位、规划、控制和目标检测），与 [Gazebo Classic](../sim_gazebo_classic/index.md) 仿真器完全集成。