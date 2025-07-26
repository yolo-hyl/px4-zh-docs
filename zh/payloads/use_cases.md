# 载荷使用案例

本主题列出一些常见的无人机"载荷使用案例"，以及PX4如何支持这些应用。

## 地图无人机

地图无人机通过相机在调查过程中以时间或距离间隔捕获图像。  
PX4支持通过[MAVLink](../camera/mavlink_v2_camera.md)或[飞控输出](../camera/fc_connected_camera.md)连接的相机。  
两种相机类型均支持地图应用，但通过不同的MAVLink命令/任务项实现。

## 货运无人机（包裹投递）

货运无人机通常使用夹具、绞盘和其他装置在目的地释放包裹。  
PX4支持通过任务中的[夹具](../peripherals/gripper.md)实现包裹投递。  
夹具也可以通过[MAV_CMD_DO_GRIPPER](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GRIPPER) MAVLink命令触发，或通过摇杆按钮手动触发。

设置和使用信息请参见：

- [夹具](../peripherals/gripper.md)
- [飞行 > 包裹投递任务规划](../flying/package_delivery_mission.md)

::: info
也计划支持绞盘和其他释放机制。

如果需要使用尚未集成的硬件进行货运投递，可以使用[通用执行器控制](../payloads/generic_actuator_control.md)。
:::

## 监视、搜索与救援

监视和搜索与救援无人机与地图无人机有相似需求。  
主要区别在于，除了执行规划的调查区域外，它们通常需要对相机进行独立控制以进行图像和视频采集，并可能需要在白天和夜间工作。

使用支持[MAVLink相机协议](https://mavlink.io/en/services/camera.html)的[MAVLink](../camera/mavlink_v2_camera.md)，这可以实现图像和视频采集、变焦、存储管理、同一机体使用多台相机并切换等。  
这些相机可以通过QGroundControl手动控制，或通过MAVSDK在[独立相机操作](https://mavsdk.mavlink.io/main/en/cpp/api_reference/classmavsdk_1_1_camera.html)和[任务](https://mavsdk.mavlink.io/main/en/cpp/api_reference/structmavsdk_1_1_mission_1_1_mission_item.html#structmavsdk_1_1_mission_1_1_mission_item_1a0299fbbe7c7b03bc43eb116f96b48df4)中控制。  
关于如何配置相机与MAVLink配合使用的说明，请参见[MAVLink相机](../camera/mavlink_v2_camera.md)。

::: info
直接连接飞控的相机仅支持触发功能，可能不适合大多数监视/搜索工作。
:::

搜索与救援无人机可能还需要携带货物，例如为受困徒步者提供紧急物资。  
关于载荷投递的信息，请参见上方的[货运无人机](#cargo-drones-package-delivery)部分。

## 农业无人机/作物喷洒

农业无人机常用于作物健康测绘、病虫害检测和动物管理（放牧、追踪等）。  
这些案例与[地图](#地图无人机)和[监视、搜索与救援](#surveillance-search-rescue)案例类似。  
虽然特定作物/动物可能需要专用相机，但与PX4的集成方式相同。

农业无人机也可能用于作物喷洒。  
在此情况下，喷洒器必须作为[通用执行器](../payloads/generic_actuator_control.md)控制：

- [通用执行器控制](../payloads/generic_actuator_control.md#generic-actuator-control-with-mavlink)说明如何将飞控输出连接到喷洒器，以便通过MAVLink控制。
  大多数喷洒器提供激活/停用泵的控制；一些还允许控制流量速率或喷洒范围（例如通过控制喷嘴形状，或使用旋转器分散载荷）。
- 可以通过[调查模式](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/plan_view/pattern_survey.html)定义喷洒区域，也可以通过航点定义飞行网格。
  在任何情况下，都必须确保机体飞行路径和高度为所使用的特定喷洒剂提供足够的覆盖范围。
- 应在调查模式前后添加["设置执行器"任务项](../payloads/generic_actuator_control.md#generic-actuator-control-in-missions)，以启用和停用喷洒器。