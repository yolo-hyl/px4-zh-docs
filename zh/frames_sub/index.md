# 水下无人机（Unmanned Underwater Vehicles - UUV）

<LinkedBadge type="warning" text="Experimental" url="../airframes/#experimental-vehicles"/>

:::warning
UUV的支持为[实验性](../airframes/index.md#experimental-vehicles)功能。
欢迎志愿者维护者、[贡献](../contribute/index.md)新功能、新机体配置或其他改进！

撰写时仅在ROS的外部模式下进行了测试。
以下功能尚未实现：

- 任务模式、深度保持、稳定手动控制等模式
- BlueRobotics夹爪支持

:::

PX4对UUV提供基本支持。

## 支持的机体

PX4支持多种水下无人机（UUV）机体。
支持的配置集可见于[机体参考 > 水下机器人](../airframes/airframe_reference.md#underwater-robot)。

### PX4兼容（完全组装）

该部分列出完全组装的机体，您可更新软件以运行PX4。

- [BlueROV2](../frames_sub/bluerov2.md): 矢量6自由度UUV

### 其他机体

- HippoCampus UUV: [机体参考](../airframes/airframe_reference.md#underwater_robot_underwater_robot_hippocampus_uuv_%28unmanned_underwater_vehicle%29), [Gazebo Classic仿真](../sim_gazebo_classic/vehicles.md#hippocampus-tuhh-uuv)

## 视频

<lite-youtube videoid="1sUaURmlmT8" title="PX4 on BlueRov Demo"/>

---

<lite-youtube videoid="xSXSoUK-iBM" title="Hippocampus UUV in PX4 SITL Gazebo"/>