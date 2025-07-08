# 摄像头

摄像头对于许多[载荷应用场景](../payloads/use_cases.md)至关重要，包括地图测绘、监视、搜索与救援、作物健康监测与病虫害检测等。  
它们通常安装在[云台](../advanced/gimbal_control.md)上，云台可以提供摄像头稳定、目标跟踪以及相对于机体的独立移动功能。

## 摄像头类型

PX4支持三种类型的摄像头：

- [MAVLink摄像头](../camera/mavlink_v2_camera.md)：支持[摄像头协议v2](https://mavlink.io/en/services/camera.html) (**RECOMMENDED**)。
- [简易MAVLink摄像头](../camera/mavlink_v1_camera.md)：支持较旧的[摄像头协议v1](https://mavlink.io/en/services/camera.html)。
- [连接至飞控输出的摄像头](../camera/fc_connected_camera.md)：通过[摄像头协议v1](https://mavlink.io/en/services/camera.html)控制。

推荐使用[MAVLink摄像头](../camera/mavlink_v2_camera.md)，因为其通过简单且一致的命令/消息集提供了最广泛的摄像头功能访问。  
如果摄像头不支持该协议，可在伴飞电脑上运行[camera manager](../camera/mavlink_v2_camera.md#camera-managers)作为MAVLink与摄像头原生协议之间的接口。

## 参见

- [云台（Camera Mount）](../advanced/gimbal_control.md)
- [摄像头集成/架构](../camera/camera_architecture.md)（PX4开发者）