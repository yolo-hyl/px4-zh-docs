# 使用运动捕捉系统飞行（VICON、NOKOV、Optitrack）

:::warning
**正在开发中**

本主题与[外部位置估计（ROS）](../ros/external_position_estimation.md)存在大量重叠内容。
:::

室内运动捕捉系统（如VICON、NOKOV和Optitrack）可用于为机体状态估计提供位置和姿态数据，或作为分析时的真实值参考。运动捕捉数据可用于更新PX4相对于本地原点的局部位置估计。运动捕捉系统的航向（偏航角）也可由姿态估计器选择性集成。

运动捕捉系统的姿态（位置和方向）数据通过MAVLink协议发送至自动驾驶仪，使用[ATT_POS_MOCAP](https://mavlink.io/en/messages/common.html#ATT_POS_MOCAP)消息。详见下方坐标系章节的数据表示规范。[mavros](../ros/mavros_installation.md) ROS-Mavlink接口包含默认插件用于发送该消息。也可通过纯C/C++代码和直接调用MAVLink库实现。

## 计算架构

**强烈建议**通过**机载**计算机（如Raspberry Pi、ODroid等）发送运动捕捉数据以实现可靠通信。机载计算机可通过WiFi连接至运动捕捉计算机，提供可靠且高带宽的连接。

大多数标准遥测链路（如3DR/SiK无线电）**不适合**高带宽的运动捕捉应用。

## 坐标系

本章节展示如何配置正确的参考坐标系。存在多种表示方式，但我们将使用以下两种：ENU和NED。

- ENU为地面固定坐标系，**X**轴指向东，**Y**轴指北，**Z**轴向上。机体坐标系中**X**轴朝前，**Z**轴向上，**Y**轴朝左。
- NED中**X**轴指北，**Y**轴向东，**Z**轴向下。机体坐标系中**X**轴朝前，**Z**轴向下，**Y**轴对应。

下图展示坐标系（左侧为NED，右侧为ENU）：

![参考坐标系](../../assets/lpe/ref_frames.png)

使用外部航向估计时，磁北将被忽略，并通过与世界_x_轴对应的向量模拟（可在运动捕捉校准中自由设置）；偏航角将相对于局部_x_轴给出。

:::warning
在运动捕捉软件中创建刚体时，请先将机体与世界**X**轴对齐，否则航向估计将存在初始偏移。
:::

## 估计算法选择

GPS功能系统推荐使用EKF2（LPE已被弃用，不再支持和维护）。无GPS时推荐使用Q-Estimator，因其无需磁力计或气压计即可运行。

详见[切换状态估计算法](../advanced/switching_state_estimators.md)。

### EKF2

运动捕捉系统的ROS话题为`mocap_pose_estimate`，视觉系统的为`vision_pose_estimate`。
详情请参阅[mavros_extras](http://wiki.ros.org/mavros_extras)。

## 测试

## 故障排查