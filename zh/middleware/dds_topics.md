# dds_topics.yaml — 用于ROS 2的PX4主题

::: 注意
本文档由源代码[自动生成](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/msg/generate_msg_docs.py)。
:::

[dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 文件定义了当 [PX4被构建](../middleware/uxrce_dds.md#code-generation) 时，哪些 uORB 消息定义会被编译进 [uxrce_dds_client](../modules/modules_system.md#uxrce-dds-client) 模块，因此决定了哪些主题默认可供 ROS 2 应用程序订阅或发布。

本文档展示了 [dds_topics.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/uxrce_dds_client/dds_topics.yaml) 的 Markdown 渲染版本，列出了发布、订阅等信息。

## Publications

话题 | 类型 | 速率限制
--- | --- | ---
`/fmu/out/register_ext_component_reply` | [px4_msgs::msg::RegisterExtComponentReply](../msg_docs/RegisterExtComponentReply.md) | 
`/fmu/out/arming_check_request` | [px4_msgs::msg::ArmingCheckRequest](../msg_docs/ArmingCheckRequest.md) | 5.0
`/fmu/out/mode_completed` | [px4_msgs::msg::ModeCompleted](../msg_docs/ModeCompleted.md) | 50.0
`/fmu/out/battery_status` | [px4_msgs::msg::BatteryStatus](../msg_docs/BatteryStatus.md) | 1.0
`/fmu/out/collision_constraints` | [px4_msgs::msg::CollisionConstraints](../msg_docs/CollisionConstraints.md) | 50.0
`/fmu/out/estimator_status_flags` | [px4_msgs::msg::EstimatorStatusFlags](../msg_docs/EstimatorStatusFlags.md) | 5.0
`/fmu/out/failsafe_flags` | [px4_msgs::msg::FailsafeFlags](../msg_docs/FailsafeFlags.md) | 5.0
`/fmu/out/manual_control_setpoint` | [px4_msgs::msg::ManualControlSetpoint](../msg_docs/ManualControlSetpoint.md) | 25.0
`/fmu/out/message_format_response` | [px4_msgs::msg::MessageFormatResponse](../msg_docs/MessageFormatResponse.md) | 
`/fmu/out/position_setpoint_triplet` | [px4_msgs::msg::PositionSetpointTriplet](../msg_docs/PositionSetpointTriplet.md) | 5.0
`/fmu/out/sensor_combined` | [px4_msgs::msg::SensorCombined](../msg_docs/SensorCombined.md) | 
`/fmu/out/timesync_status` | [px4_msgs::msg::TimesyncStatus](../msg_docs/TimesyncStatus.md) | 10.0
`/fmu/out/vehicle_land_detected` | [px4_msgs::msg::VehicleLandDetected](../msg_docs/VehicleLandDetected.md) | 5.0
`/fmu/out/vehicle_attitude` | [px4_msgs::msg::VehicleAttitude](../msg_docs/VehicleAttitude.md) | 
`/fmu/out/vehicle_control_mode` | [px4_msgs::msg::VehicleControlMode](../msg_docs/VehicleControlMode.md) | 50.0
`/fmu/out/vehicle_command_ack` | [px4_msgs::msg::VehicleCommandAck](../msg_docs/VehicleCommandAck.md) | 
`/fmu/out/vehicle_global_position` | [px4_msgs::msg::VehicleGlobalPosition](../msg_docs/VehicleGlobalPosition.md) | 50.0
`/fmu/out/vehicle_gps_position` | [px4_msgs::msg::SensorGps](../msg_docs/SensorGps.md) | 50.0
`/fmu/out/vehicle_local_position` | [px4_msgs::msg::VehicleLocalPosition](../msg_docs/VehicleLocalPosition.md) | 50.0
`/fmu/out/vehicle_odometry` | [px4_msgs::msg::VehicleOdometry](../msg_docs/VehicleOdometry.md) | 
`/fmu/out/vehicle_status` | [px4_msgs::msg::VehicleStatus](../msg_docs/VehicleStatus.md) | 5.0
`/fmu/out/airspeed_validated` | [px4_msgs::msg::AirspeedValidated](../msg_docs/AirspeedValidated.md) | 50.0
`/fmu/out/vtol_vehicle_status` | [px4_msgs::msg::VtolVehicleStatus](../msg_docs/VtolVehicleStatus.md) | 
`/fmu/out/home_position` | [px4_msgs::msg::HomePosition](../msg_docs/HomePosition.md) | 5.0## 订阅

Topic | 类型
--- | ---
/fmu/in/register_ext_component_request | [px4_msgs::msg::RegisterExtComponentRequest](../msg_docs/RegisterExtComponentRequest.md)
/fmu/in/unregister_ext_component | [px4_msgs::msg::UnregisterExtComponent](../msg_docs/UnregisterExtComponent.md)
/fmu/in/config_overrides_request | [px4_msgs::msg::ConfigOverrides](../msg_docs/ConfigOverrides.md)
/fmu/in/arming_check_reply | [px4_msgs::msg::ArmingCheckReply](../msg_docs/ArmingCheckReply.md)
/fmu/in/message_format_request | [px4_msgs::msg::MessageFormatRequest](../msg_docs/MessageFormatRequest.md)
/fmu/in/mode_completed | [px4_msgs::msg::ModeCompleted](../msg_docs/ModeCompleted.md)
/fmu/in/config_control_setpoints | [px4_msgs::msg::VehicleControlMode](../msg_docs/VehicleControlMode.md)
/fmu/in/distance_sensor | [px4_msgs::msg::DistanceSensor](../msg_docs/DistanceSensor.md)
/fmu/in/manual_control_input | [px4_msgs::msg::ManualControlSetpoint](../msg_docs/ManualControlSetpoint.md)
/fmu/in/offboard_control_mode | [px4_msgs::msg::OffboardControlMode](../msg_docs/OffboardControlMode.md)
/fmu/in/onboard_computer_status | [px4_msgs::msg::OnboardComputerStatus](../msg_docs/OnboardComputerStatus.md)
/fmu/in/obstacle_distance | [px4_msgs::msg::ObstacleDistance](../msg_docs/ObstacleDistance.md)
/fmu/in/sensor_optical_flow | [px4_msgs::msg::SensorOpticalFlow](../msg_docs/SensorOpticalFlow.md)
/fmu/in/goto_setpoint | [px2_msgs::msg::GotoSetpoint](../msg_docs/GotoSetpoint.md)
/fmu/in/telemetry_status | [px4_msgs::msg::TelemetryStatus](../msg_docs/TelemetryStatus.md)
/fmu/in/trajectory_setpoint | [px4_msgs::msg::TrajectorySetpoint](../msg_docs/TrajectorySetpoint.md)
/fmu/in/vehicle_attitude_setpoint | [px4_msgs::msg::VehicleAttitudeSetpoint](../msg_docs/VehicleAttitudeSetpoint.md)
/fmu/in/vehicle_mocap_odometry | [px4_msgs::msg::VehicleOdometry](../msg_docs/VehicleOdometry.md)
/fmu/in/vehicle_rates_setpoint | [px4_msgs::msg::VehicleRatesSetpoint](../msg_docs/VehicleRatesSetpoint.md)
/fmu/in/vehicle_visual_odometry | [px4_msgs::msg::VehicleOdometry](../msg_docs/VehicleOdometry.md)
/fmu/in/vehicle_command | [px4_msgs::msg::VehicleCommand](../msg_docs/VehicleCommand.md)
/fmu/in/vehicle_command_mode_executor | [px4_msgs::msg::VehicleCommand](../msg_docs/VehicleCommand.md)
/fmu/in/vehicle_thrust_setpoint | [px4_msgs::msg::VehicleThrustSetpoint](../msg_docs/VehicleThrustSetpoint.md)
/fmu/in/vehicle_torque_setpoint | [px4_msgs::msg::VehicleTorqueSetpoint](../msg_docs/VehicleTorqueSetpoint.md)
/fmu/in/actuator_motors | [px4_msgs::msg::ActuatorMotors](../msg_docs/ActuatorMotors.md)
/fmu/in/actuator_servos | [px4_msgs::msg::ActuatorServos](../msg_docs/ActuatorServos.md)
/fmu/in/aux_global_position | [px4_msgs::msg::VehicleGlobalPosition](../msg_docs/VehicleGlobalPosition.md)
/fmu/in/fixed_wing_longitudinal_setpoint | [px4_msgs::msg::FixedWingLongitudinalSetpoint](../msg_docs/FixedWingLongitudinalSetpoint.md)
/fmu/in/fixed_wing_lateral_setpoint | [px4_msgs::msg::FixedWingLateralSetpoint](../msg_docs/FixedWingLateralSetpoint.md)
/fmu/in/longitudinal_control_configuration | [px4_msgs::msg::LongitudinalControlConfiguration](../msg_docs/LongitudinalControlConfiguration.md)
/fmu/in/lateral_control_configuration | [px4_msgs::msg::LateralControlConfiguration](../msg_docs/LateralControlConfiguration.md)

## 订阅多

无## 未导出

这些消息未在yaml文件中列出。
它们未构建到模块中，因此既未发布也未订阅。

::: details 查看消息

- [传感器校正](../msg_docs/SensorCorrection.md)
- [执行器输出](../msg_docs/ActuatorOutputs.md)
- [固定翼跑道控制](../msg_docs/FixedWingRunwayControl.md)
- [估计器创新值](../msg_docs/EstimatorInnovations.md)
- [飞行阶段估计](../msg_docs/FlightPhaseEstimation.md)
- [纯追踪状态](../msg_docs/PurePursuitStatus.md)
- [PX4io状态](../msg_docs/Px4ioStatus.md)
- [卫星信息](../msg_docs/SatelliteInfo.md)
- [地理围栏结果](../msg_docs/GeofenceResult.md)
- [云台管理器状态](../msg_docs/GimbalManagerStatus.md)
- [手动控制开关](../msg_docs/ManualControlSwitches.md)
- [系统电源](../msg_docs/SystemPower.md)
- [执行器控制状态](../msg_docs/ActuatorControlsStatus.md)
- [传感器状态IMU](../msg_docs/SensorsStatusImu.md)
- [估计器状态](../msg_docs/EstimatorStates.md)
- [传感器陀螺仪](../msg_docs/SensorGyro.md)
- [传感器气流](../msg_docs/SensorAirflow.md)
- [按钮事件](../msg_docs/ButtonEvent.md)
- [调试键值](../msg_docs/DebugKeyValue.md)
- [GPIO配置](../msg_docs/GpioConfig.md)
- [相机触发](../msg_docs/CameraTrigger.md)
- [起落架轮子](../msg_docs/LandingGearWheel.md)
- [机体约束](../msg_docs/VehicleConstraints.md)
- [健康报告](../msg_docs/HealthReport.md)
- [电源按钮状态](../msg_docs/PowerButtonState.md)
- [无线电状态](../msg_docs/RadioStatus.md)
- [传感器陀螺仪FIFO](../msg_docs/SensorGyroFifo.md)
- [估计器偏置](../msg_docs/EstimatorBias.md)
- [调试向量](../msg_docs/DebugVect.md)
- [距离传感器模式变更请求](../msg_docs/DistanceSensorModeChangeRequest.md)
- [返航时间估计](../msg_docs/RtlTimeEstimate.md)
- [PPS捕获](../msg_docs/PpsCapture.md)
- [传感器选择](../msg_docs/SensorSelection.md)
- [系统电源](../msg_docs/SystemPower.md)
- [执行器控制状态](../msg_docs/ActuatorControlsStatus.md)
- [传感器陀螺仪FFT](../msg_docs/SensorGyroFft.md)
- [机体气动数据](../msg_docs/VehicleAirData.md)
- [追踪目标估计器](../msg_docs/FollowTargetEstimator.md)
- [参数设置使用请求](../msg_docs/ParameterSetUsedRequest.md)
- [GPIO请求](../msg_docs/GpioRequest.md)
- [OpenDroneID操作员ID](../msg_docs/OpenDroneIdOperatorId.md)
- [返航状态](../msg_docs/RtlStatus.md)
- [空速](../msg_docs/Airspeed.md)
- [机体加速度](../msg_docs/VehicleAcceleration.md)
- [参数设置值请求](../msg_docs/ParameterSetValueRequest.md)
- [红外锁报告](../msg_docs/IrlockReport.md)
- [加热器状态](../msg_docs/HeaterStatus.md)
- [ADC报告](../msg_docs/AdcReport.md)
- [PWM输入](../msg_docs/PwmInput.md)
- [倾转旋翼额外控制](../msg_docs/TiltrotorExtraControls.md)
- [估计器辅助源1D](../msg_docs/EstimatorAidSource1d.md)
- [中等测试](../msg_docs/OrbTestMedium.md)
- [机体姿态设定点V0](../msg_docs/VehicleAttitudeSetpointV0.md)
- [估计器辅助源2D](../msg_docs/EstimatorAidSource2d.md)
- [调音控制](../msg_docs/TuneControl.md)
- [轮速编码器](../msg_docs/WheelEncoders.md)
- [自动调参姿态控制状态](../msg_docs/AutotuneAttitudeControlStatus.md)
- [着陆目标创新值](../msg_docs/LandingTargetInnovations.md)
- [传感器状态](../msg_docs/SensorsStatus.md)
:::