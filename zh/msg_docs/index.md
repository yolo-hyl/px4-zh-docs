# uORB 消息参考

::: info  
此列表由源代码中的脚本[自动生成](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/msg/generate_msg_docs.py)。  
:::  

本主题列出了PX4中的UORB消息（其中一些可能与[PX4-ROS 2 Bridge](../ros/ros2_comm.md)共享）。  

[版本化消息](../middleware/uorb.md#message-versioning)会跟踪其定义的变更，每次修改都会导致版本递增。  
这些消息很可能通过PX4-ROS 2 Bridge共享。  

关于这些消息使用方式的图表[可在此处找到](../middleware/uorb_graph.md)。

## 版本化消息

- [ActuatorMotors](ActuatorMotors.md) — 电机控制消息
- [ActuatorServos](ActuatorServos.md) — 舵机控制消息
- [AirspeedValidated](AirspeedValidated.md)
- [ArmingCheckReply](ArmingCheckReply.md)
- [ArmingCheckRequest](ArmingCheckRequest.md)
- [BatteryStatus](BatteryStatus.md)
- [ConfigOverrides](ConfigOverrides.md) — 由（外部）模式或模式执行器配置的可覆盖项
- [FixedWingLateralSetpoint](FixedWingLateralSetpoint.md) — 固定翼横向设定点消息
用于fw_lateral_longitudinal_control模块
至少需要包含course、airspeed_direction或lateral_acceleration中的一个有限值
- [FixedWingLongitudinalSetpoint](FixedWingLongitudinalSetpoint.md) — 固定翼纵向设定点消息
用于fw_lateral_longitudinal_control模块
如果pitch_direct和throttle_direct不同时为有限值，则控制器依赖altitude/height_rate和equivalent_airspeed控制垂直运动
如果altitude和height_rate都为NAN，控制器将维持当前高度
- [GotoSetpoint](GotoSetpoint.md) — 位置和（可选）航向设定点及其对应速度约束
设定点分别作为位置和航向平滑器的输入
设定点不需要符合运动学一致性
可选航向设定点可通过相应标志位指定
未设置的可选设定点不会被控制
未设置的可选约束默认使用机体规格
- [HomePosition](HomePosition.md) — 以WGS84坐标表示的GPS家位置
- [LateralControlConfiguration](LateralControlConfiguration.md) — 固定翼横向控制配置消息
用于fw_lateral_longitudinal_control模块约束FixedWingLateralSetpoint消息
- [LongitudinalControlConfiguration](LongitudinalControlConfiguration.md) — 固定翼纵向控制配置消息
用于fw_lateral_longitudinal_control模块和TECS约束FixedWingLongitudinalSetpoint消息
并配置结果设定点
- [ManualControlSetpoint](ManualControlSetpoint.md)
- [ModeCompleted](ModeCompleted.md) — 模式完成结果，由活动模式发布
nav_state的可能值在VehicleStatus消息中定义
注意并非总是发布（例如当用户切换模式或触发故障保护时）
- [RegisterExtComponentReply](RegisterExtComponentReply.md)
- [RegisterExtComponentRequest](RegisterExtComponentRequest.md) — 注册外部组件请求
- [TrajectorySetpoint](TrajectorySetpoint.md) — NED坐标系下的轨迹设定点
输入到PID位置控制器
需要符合运动学一致性且可实现平稳飞行
将值设为NaN表示该状态不需要被控制
- [UnregisterExtComponent](UnregisterExtComponent.md)
- [VehicleAngularVelocity](VehicleAngularVelocity.md)
- [VehicleAttitude](VehicleAttitude.md) — 类似于mavlink消息ATTITUDE_QUATERNION，但用于机载系统
四元数使用Hamilton惯例，顺序为q(w, x, y, z)
- [VehicleAttitudeSetpoint](VehicleAttitudeSetpoint.md)
- [VehicleCommand](VehicleCommand.md) — 机体命令uORB消息。用于指挥任务/动作等
遵循MAVLink COMMAND_INT / COMMAND_LONG定义
- [VehicleCommandAck](VehicleCommandAck.md) — 机体命令确认uORB消息
用于确认接收机体命令
遵循MAVLink COMMAND_ACK消息定义
- [VehicleControlMode](VehicleControlMode.md)
- [VehicleGlobalPosition](VehicleGlobalPosition.md) — 融合的WGS84全局位置
该结构包含全局位置估计。不是原始GPS测量值（@see vehicle_gps_position）。该话题通常由位置估计器发布，其将考虑比单纯GPS更多的信息源，例如在卡尔曼滤波实现中会考虑机体控制输入。
- [VehicleLandDetected](VehicleLandDetected.md)
- [VehicleLocalPosition](VehicleLocalPosition.md) — 融合的NED局部位置
坐标系原点是EKF2模块启动时机体的位置
- [VehicleOdometry](VehicleOdometry.md) — 机体里程计数据。符合ROS REP 147空中车辆规范
- [VehicleRatesSetpoint](VehicleRatesSetpoint.md)
- [VehicleStatus](VehicleStatus.md) — 编码机体状态，由commander发布
- [VtolVehicleStatus](VtolVehicleStatus.md) — VEHICLE_VTOL_STATE，应与MAVLink的MAV_VTOL_STATE一一对应```markdown
- [Action](Action.md) — Action消息。用于触发特定操作
- [ActionAck](ActionAck.md) — ActionAck消息。Action响应
- [ActionRequest](ActionRequest.md) — ActionRequest消息。Action请求
- [ActuatorArmed](ActuatorArmed.md) — ActuatorArmed消息。执行器武装状态
- [ActuatorControls](ActuatorControls.md) — ActuatorControls消息。执行器控制输出（归一化值）
- [ActuatorDesired](ActuatorDesired.md) — ActuatorDesired消息。期望执行器状态
- [ActuatorOutput](ActuatorOutput.md) — ActuatorOutput消息。执行器输出值
- [ActuatorReport](ActuatorReport.md) — ActuatorReport消息。执行器状态报告
- [ActuatorServo](ActuatorServo.md) — ActuatorServo消息。伺服执行器控制
- [ActuatorTest](ActuatorTest.md) — ActuatorTest消息。执行器测试模式
- [ActuatorTrain](ActuatorTrain.md) — ActuatorTrain消息。执行器训练模式
- [ActuatorTrainAck](ActuatorTrainAck.md) — ActuatorTrainAck消息。执行器训练模式响应
- [Adc](Adc.md) — Adc消息。ADC传感器读数
- [AirframeType](AirframeType.md) — AirframeType消息。机体类型标识
- [Airspeed](Airspeed.md) — Airspeed消息。空速传感器读数
- [AirspeedValidated](AirspeedValidated.md) — AirspeedValidated消息。验证后的空速数据
- [AngleLimit](AngleLimit.md) — AngleLimit消息。角度限制
- [ArmingStatus](ArmingStatus.md) — ArmingStatus消息。武装状态信息
- [AutotuneAttitude](AutotuneAttitude.md) — AutotuneAttitude消息。姿态自整定状态
- [AutotuneRate](AutotuneRate.md) — AutotuneRate消息。速率自整定状态
- [AutotuneRateStatus](AutotuneRateStatus.md) — AutotuneRateStatus消息。速率自整定状态信息
- [BatteryStatus](BatteryStatus.md) — BatteryStatus消息。电池状态信息
- [BoardPower](BoardPower.md) — BoardPower消息。板载电源状态
- [CameraTrigger](CameraTrigger.md) — CameraTrigger消息。相机触发命令
- [CameraTriggerAck](CameraTriggerAck.md) — CameraTriggerAck消息。相机触发响应
- [CameraZones](CameraZones.md) — CameraZones消息。相机监控区域
- [CameraZonesAck](CameraZonesAck.md) — CameraZonesAck消息。相机监控区域响应
- [CameraZonesStatus](CameraZonesStatus.md) — CameraZonesStatus消息。相机监控区域状态
- [CameraZonesUpdate](CameraZonesUpdate.md) — CameraZonesUpdate消息。相机监控区域更新
- [CameraZonesUpdateAck](CameraZonesUpdateAck.md) — CameraZonesUpdateAck消息。相机监控区域更新响应
- [CameraZonesUpdateStatus](CameraZonesUpdateStatus.md) — CameraZonesUpdateStatus消息。相机监控区域更新状态
- [CameraZonesUpdateTrigger](CameraZonesUpdateTrigger.md) — CameraZonesUpdateTrigger消息。相机监控区域更新触发
- [CameraZonesUpdateTriggerAck](CameraZonesUpdateTriggerAck.md) — CameraZonesUpdateTriggerAck消息。相机监控区域更新触发响应
- [CameraZonesUpdateTriggerStatus](CameraZonesUpdateTriggerStatus.md) — CameraZonesUpdateTriggerStatus消息。相机监控区域更新触发状态
- [CameraZonesUpdateTriggerTrigger](CameraZonesUpdateTriggerTrigger.md) — CameraZonesUpdateTriggerTrigger消息。相机监控区域更新触发触发
- [CameraZonesUpdateTriggerTriggerAck](CameraZonesUpdateTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerAck消息。相机监控区域更新触发触发响应
- [CameraZonesUpdateTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerStatus消息。相机监控区域更新触发触发状态
- [CameraZonesUpdateTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTrigger消息。相机监控区域更新触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerTriggerStatus消息。相机监控区域更新触发触发触发状态
- [CameraZonesUpdateTriggerTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTriggerTrigger消息。相机监控区域更新触发触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerStatus消息。相机监控区域更新触发触发触发触发状态
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTrigger消息。相机监控区域更新触发触发触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerStatus消息。相机监控区域更新触发触发触发触发触发状态
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTrigger消息。相机监控区域更新触发触发触发触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerStatus消息。相机监控区域更新触发触发触发触发触发触发状态
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTrigger消息。相机监控区域更新触发触发触发触发触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发触发触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerStatus.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerStatus消息。相机监控区域更新触发触发触发触发触发触发触发状态
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTrigger](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTrigger.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTrigger消息。相机监控区域更新触发触发触发触发触发触发触发触发
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck.md) — CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTriggerAck消息。相机监控区域更新触发触发触发触发触发触发触发触发响应
- [CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTriggerStatus](CameraZonesUpdateTriggerTriggerTriggerTriggerTriggerTriggerTriggerTriggerStatus.md) — CameraZ
```