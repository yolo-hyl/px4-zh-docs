

# uORB 消息参考

::: info
此列表[自动生成](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/msg/generate_msg_docs.py)于源代码。
:::

本主题列出PX4中可用的uORB消息（其中一些可能通过[PX4-ROS 2 Bridge](../ros/ros2_comm.md)共享）。

[带版本的消息](../middleware/uorb.md#message-versioning)会跟踪其定义的变更，每次修改都会导致版本递增。
这些消息很可能通过PX4-ROS 2 Bridge进行共享。

展示如何使用这些消息的图表[可在此处找到](../middleware/uorb_graph.md)。

## 版本化消息

- [执行器电机](ActuatorMotors.md) — 电机控制消息
- [执行器舵机](ActuatorServos.md) — 舵机控制消息
- [空速验证](AirspeedValidated.md)
- [解锁检查回复](ArmingCheckReply.md)
- [解锁检查请求](ArmingCheckRequest.md)
- [电池状态](BatteryStatus.md)
- [配置覆盖](ConfigOverrides.md) — 可由(外部)模式或模式执行器配置的覆盖选项
- [固定翼横向设定点](FixedWingLateralSetpoint.md) — 固定翼横向设定点消息
由 fw_lateral_longitudinal_control 模块使用
至少需要 course、airspeed_direction 或 lateral_acceleration 中的一个为有限值
- [固定翼纵向设定点](FixedWingLongitudinalSetpoint.md) — 固定翼纵向设定点消息
由 fw_lateral_longitudinal_control 模块使用
若 pitch_direct 和 throttle_direct 不同时为有限值，则控制器依赖高度/高度变化率和等效空速控制垂直运动
当 altitude 和 height_rate 均为 NAN 时，控制器维持当前高度
- [目标设定点](GotoSetpoint.md) — 位置和（可选）航向设定点及其对应的速度约束
设定点作为位置和航向平滑器的输入
设定点不需要运动学一致性
可选航向设定点可通过相应标志位控制
未设置的可选设定点不会被控制
未设置的可选约束默认采用机体规格
- [家位置](HomePosition.md) — GPS 家位置的 WGS84 坐标
- [横向控制配置](LateralControlConfiguration.md) — 固定翼横向控制配置消息
由 fw_lateral_longitudinal_control 模块用于约束 FixedWingLateralSetpoint 消息
- [纵向控制配置](LongitudinalControlConfiguration.md) — 固定翼纵向控制配置消息
由 fw_lateral_longitudinal_control 模块和 TECS 用于约束 FixedWingLongitudinalSetpoint 消息
并配置最终的设定点
- [手动控制设定点](ManualControlSetpoint.md)
- [模式完成](ModeCompleted.md) — 模式完成结果，由活动模式发布
nav_state 可能的值在 VehicleStatus 消息中定义
注意这并非总是发布（例如用户切换模式或触发保护时）
- [注册扩展组件回复](RegisterExtComponentReply.md)
- [注册扩展组件请求](RegisterExtComponentRequest.md) — 注册外部组件的请求
- [轨迹设定点](TrajectorySetpoint.md) — NED 坐标系下的轨迹设定点
PID 位置控制器的输入
需要运动学一致性且具备平滑飞行的可行性
将值设置为 NaN 表示该状态不应被控制
- [注销扩展组件](UnregisterExtComponent.md)
- [机体角速度](VehicleAngularVelocity.md)
- [机体姿态](VehicleAttitude.md) — 与 mavlink 消息 ATTITUDE_QUATERNION 类似，但用于机载系统
四元数使用 Hamilton 约定，顺序为 q(w, x, y, z)
- [机体姿态设定点](VehicleAttitudeSetpoint.md)
- [机体指令](VehicleCommand.md) — 机体指令 uORB 消息。用于指挥任务/动作等
遵循 MAVLink COMMAND_INT / COMMAND_LONG 定义
- [机体指令确认](VehicleCommandAck.md) — 机体指令确认 uORB 消息
用于确认已接收机体指令
遵循 MAVLink COMMAND_ACK 消息定义
- [机体控制模式](VehicleControlMode.md)
- [机体全局位置](VehicleGlobalPosition.md) — 融合后的 WGS84 全局位置
该结构包含全局位置估计。这不是原始 GPS 测量数据 (@see vehicle_gps_position)。该主题通常由位置估计器发布，其会考虑比 GPS 更多的信息来源，例如在卡尔曼滤波实现中考虑机体的控制输入
- [着陆检测](VehicleLandDetected.md)
- [机体局部位置](VehicleLocalPosition.md) — 融合后的 NED 局部位置
坐标系原点为 EKF2 模块启动时的机体位置
- [机体里程计](VehicleOdometry.md) — 机体里程计数据。符合 ROS REP 147 用于空中平台
- [机体角速度设定点](VehicleRatesSetpoint.md)
- [机体状态](VehicleStatus.md) — 编码由 commander 发布的机体系统状态
- [VTOL机体状态](VtolVehicleStatus.md) — VEHICLE_VTOL_STATE，应与 MAVLink 的 MAV_VTOL_STATE 1:1 匹配


## Unversioned Messages

- [ActionRequest](ActionRequest.md)
- [ActuatorArmed](ActuatorArmed.md)
- [ActuatorControlsStatus](ActuatorControlsStatus.md)
- [ActuatorOutputs](ActuatorOutputs.md)
- [ActuatorServosTrim](ActuatorServosTrim.md) — Servo trims, added as offset to servo outputs
- [ActuatorTest](ActuatorTest.md)
- [AdcReport](AdcReport.md)
- [Airspeed](Airspeed.md)
- [AirspeedWind](AirspeedWind.md)
- [AutotuneAttitudeControlStatus](AutotuneAttitudeControlStatus.md)
- [ButtonEvent](ButtonEvent.md)
- [CameraCapture](CameraCapture.md)
- [CameraStatus](CameraStatus.md)
- [CameraTrigger](CameraTrigger.md)
- [CanInterfaceStatus](CanInterfaceStatus.md)
- [CellularStatus](CellularStatus.md) — Cellular status
- [CollisionConstraints](CollisionConstraints.md) — Local setpoint constraints in NED frame
setting something to NaN means that no limit is provided
- [ControlAllocatorStatus](ControlAllocatorStatus.md)
- [Cpuload](Cpuload.md)
- [DatamanRequest](DatamanRequest.md)
- [DatamanResponse](DatamanResponse.md)
- [DebugArray](DebugArray.md)
- [DebugKeyValue](DebugKeyValue.md)
- [DebugValue](DebugValue.md)
- [DebugVect](DebugVect.md)
- [DifferentialPressure](DifferentialPressure.md)
- [DistanceSensor](DistanceSensor.md) — DISTANCE_SENSOR message data
- [DistanceSensorModeChangeRequest](DistanceSensorModeChangeRequest.md)
- [Ekf2Timestamps](Ekf2Timestamps.md) — this message contains the (relative) timestamps of the sensor inputs used by EKF2.
It can be used for reproducible replay.
- [EscReport](EscReport.md)
- [EscStatus](EscStatus.md)
- [EstimatorAidSource1d](EstimatorAidSource1d.md)
- [EstimatorAidSource2d](EstimatorAidSource2d.md)
- [EstimatorAidSource3d](EstimatorAidSource3d.md)
- [EstimatorBias](EstimatorBias.md)
- [EstimatorBias3d](EstimatorBias3d.md)
- [EstimatorEventFlags](EstimatorEventFlags.md)
- [EstimatorGpsStatus](EstimatorGpsStatus.md)
- [EstimatorInnovations](EstimatorInnovations.md)
- [EstimatorSelectorStatus](EstimatorSelectorStatus.md)
- [EstimatorSensorBias](EstimatorSensorBias.md) — Sensor readings and in-run biases in SI-unit form. Sensor readings are compensated for static offsets,
scale errors, in-run bias and thermal drift (if thermal compensation is enabled and available).
- [EstimatorStates](EstimatorStates.md)
- [EstimatorStatus](EstimatorStatus.md)
- [EstimatorStatusFlags](EstimatorStatusFlags.md)
- [Event](Event.md) — Events interface
- [FailsafeFlags](FailsafeFlags.md) — Input flags for the failsafe state machine set by the arming & health checks.
- [FailureDetectorStatus](FailureDetectorStatus.md)
- [FigureEightStatus](FigureEightStatus.md)
- [FixedWingLateralGuidanceStatus](FixedWingLateralGuidanceStatus.md) — Fixed Wing Lateral Guidance Status message
Published by fw_pos_control module to report the resultant lateral setpoints and NPFG debug outputs
- [FixedWingLateralStatus](FixedWingLateralStatus.md) — Fixed Wing Lateral Status message
Published by the fw_lateral_longitudinal_control module to report the resultant lateral setpoint
- [FixedWingRunwayControl](FixedWingRunwayControl.md) — Auxiliary control fields for fixed-wing runway takeoff/landing
- [FlightPhaseEstimation](FlightPhaseEstimation.md)
- [FollowTarget](FollowTarget.md)
- [FollowTargetEstimator](FollowTargetEstimator.md)
- [FollowTargetStatus](FollowTargetStatus.md)
- [FuelTankStatus](FuelTankStatus.md)
- [GeneratorStatus](GeneratorStatus.md)
- [GeofenceResult](GeofenceResult.md)
- [GeofenceStatus](GeofenceStatus.md)
- [GimbalControls](GimbalControls.md)
- [GimbalDeviceAttitudeStatus](GimbalDeviceAttitudeStatus.md)
- [GimbalDeviceInformation](GimbalDeviceInformation.md)
- [GimbalDeviceSetAttitude](GimbalDeviceSetAttitude.md)
- [GimbalManagerInformation](GimbalManagerInformation.md)
- [GimbalManagerSetAttitude](GimbalManagerSetAttitude.md)
- [GimbalManagerSetManualControl](GimbalManagerSetManualControl.md)
- [GimbalManagerStatus](GimbalManagerStatus.md)
- [GpioConfig](GpioConfig.md) — GPIO configuration
- [GpioIn](GpioIn.md) — GPIO mask and state
- [GpioOut](GpioOut.md) — GPIO mask and state
- [GpioRequest](GpioRequest.md) — Request GPIO mask to be read
- [GpsDump](GpsDump.md) — This message is used to dump the raw gps communication to the log.
- [GpsInjectData](GpsInjectData.md)
- [Gripper](Gripper.md) — # Used to command an actuation in the gripper, which is mapped to a specific output in the control allocation module
- [HealthReport](HealthReport.md)
- [HeaterStatus](HeaterStatus.md)
- [HoverThrustEstimate](HoverThrustEstimate.md)
- [InputRc](InputRc.md)
- [InternalCombustionEngineControl](InternalCombustionEngineControl.md)
- [InternalCombustionEngineStatus](InternalCombustionEngineStatus.md)
- [IridiumsbdStatus](IridiumsbdStatus.md)
- [IrlockReport](IrlockReport.md) — IRLOCK_REPORT message data
- [LandingGear](LandingGear.md)
- [LandingGearWheel](LandingGearWheel.md)
- [LandingTargetInnovations](LandingTargetInnovations.md)
- [LandingTargetPose](LandingTargetPose.md) — Relative position of precision land target in navigation (body fixed, north aligned, NED) and inertial (world fixed, north aligned, NED) frames
- [LaunchDetectionStatus](LaunchDetectionStatus.md) — Status of the launch detection state machine (fixed-wing only)
- [LedControl](LedControl.md) — LED control: control a single or multiple LED's.
These are the externally visible LED's, not the board LED's
- [LogMessage](LogMessage.md) — A logging message, output with PX4_WARN, PX4_ERR, PX4_INFO
- [LoggerStatus](LoggerStatus.md)
- [MagWorkerData](MagWorkerData.md)
- [MagnetometerBiasEstimate](MagnetometerBiasEstimate.md)
- [ManualControlSwitches](ManualControlSwitches.md)
- [MavlinkLog](MavlinkLog.md)
- [MavlinkTunnel](MavlinkTunnel.md) — MAV_TUNNEL_PAYLOAD_TYPE enum
- [MessageFormatRequest](MessageFormatRequest.md)
- [MessageFormatResponse](MessageFormatResponse.md)
- [Mission](Mission.md)
- [MissionResult](MissionResult.md)
- [MountOrientation](MountOrientation.md)
- [NavigatorMissionItem](NavigatorMissionItem.md)
- [NavigatorStatus](NavigatorStatus.md) — Current status of a Navigator mode
The possible values of nav_state are defined in the VehicleStatus msg.
- [NormalizedUnsignedSetpoint](NormalizedUnsignedSetpoint.md)
- [ObstacleDistance](ObstacleDistance.md) — Obstacle distances in front of the sensor.
- [OffboardControlMode](OffboardControlMode.md) — Off-board control mode
- [OnboardComputerStatus](OnboardComputerStatus.md) — ONBOARD_COMPUTER_STATUS message data
- [OpenDroneIdArmStatus](OpenDroneIdArmStatus.md)
- [OpenDroneIdOperatorId](OpenDroneIdOperatorId.md)
- [OpenDroneIdSelfId](OpenDroneIdSelfId.md)
- [OpenDroneIdSystem](OpenDroneIdSystem.md)
- [OrbTest](OrbTest.md)
- [OrbTestLarge](OrbTestLarge.md)
- [OrbTestMedium](OrbTestMedium.md)
- [OrbitStatus](OrbitStatus.md) — ORBIT_YAW_BEHAVIOUR
- [ParameterResetRequest](ParameterResetRequest.md) — ParameterResetRequest : Used by the primary to reset one or all parameter value(s) on the remote
- [ParameterSetUsedRequest](ParameterSetUsedRequest.md) — ParameterSetUsedRequest : Used by a remote to update the used flag for a parameter on the primary
- [ParameterSetValueRequest](ParameterSetValueRequest.md) — ParameterSetValueRequest : Used by a remote or primary to update the value for a parameter at the other end
- [ParameterSetValueResponse](ParameterSetValueResponse.md) — ParameterSetValueResponse : Response to a set value request by either primary or secondary
- [ParameterUpdate](ParameterUpdate.md) — This message is used to notify the system about one or more parameter changes
- [Ping](Ping.md)
- [PositionControllerLandingStatus](PositionControllerLandingStatus.md)
- [PositionControllerStatus](PositionControllerStatus.md)
- [PositionSetpoint](PositionSetpoint.md) — this file is only used in the position_setpoint triple as a dependency
- [PositionSetpointTriplet](PositionSetpointTriplet.md) — Global position setpoint triplet in WGS84 coordinates.
This are the three next waypoints (or just the next two or one).
- [PowerButtonState](PowerButtonState.md) — power button state notification message
- [PowerMonitor](PowerMonitor.md) — power monitor message
- [PpsCapture](PpsCapture.md)
- [PurePursuitStatus](PurePursuitStatus.md)
- [PwmInput](PwmInput.md)
- [Px4ioStatus](Px4ioStatus.md)
- [QshellReq](QshellReq.md)
- [QshellRetval](QshellRetval.md)
- [RadioStatus](RadioStatus.md)
- [RateCtrlStatus](RateCtrlStatus.md)
- [RcChannels](RcChannels.md)
- [RcParameterMap](RcParameterMap.md)
- [RoverAttitudeSetpoint](RoverAttitudeSetpoint.md)
- [RoverAttitudeStatus](RoverAttitudeStatus.md)
- [RoverPositionSetpoint](RoverPositionSetpoint.md)
- [RoverRateSetpoint](RoverRateSetpoint.md)
- [RoverRateStatus](RoverRateStatus.md)
- [RoverSteeringSetpoint](RoverSteeringSetpoint.md)
- [RoverThrottleSetpoint](RoverThrottleSetpoint.md)
- [RoverVelocitySetpoint](RoverVelocitySetpoint.md)
- [RoverVelocityStatus](RoverVelocityStatus.md)
- [Rpm](Rpm.md)
- [RtlStatus](RtlStatus.md)
- [RtlTimeEstimate](RtlTimeEstimate.md)
- [SatelliteInfo](SatelliteInfo.md)
- [SensorAccel](SensorAccel.md)
- [SensorAccelFifo](SensorAccelFifo.md)
- [SensorAirflow](SensorAirflow.md)
- [SensorBaro](SensorBaro.md)
- [SensorCombined](SensorCombined.md) — Sensor readings in SI-unit form.
These fields are scaled and offset-compensated where possible and do not
change with board revisions and sensor updates.
- [SensorCorrection](SensorCorrection.md) — Sensor corrections in SI-unit form for the voted sensor
- [SensorGnssRelative](SensorGnssRelative.md) — GNSS relative positioning information in NED frame. The NED frame is defined as the local topological system at the reference station.
- [SensorGps](SensorGps.md) — GPS position in WGS84 coordinates.
the field 'timestamp' is for the position & velocity (microseconds)
- [SensorGyro](SensorGyro.md)
- [SensorGyroFft](SensorGyroFft.md)
- [SensorGyroFifo](SensorGyroFifo.md)
- [SensorHygrometer](SensorHygrometer.md)
- [SensorMag](SensorMag.md)
- [SensorOpticalFlow](SensorOpticalFlow.md)
- [SensorPreflightMag](SensorPreflightMag.md) — Pre-flight sensor check metrics.
The topic will not be updated when the vehicle is armed
- [SensorSelection](SensorSelection.md) — Sensor ID's for the voted sensors output on the sensor_combined topic.
Will be updated on startup of the sensor module and when sensor selection changes
- [SensorUwb](SensorUwb.md) — UWB distance contains the distance information measured by an ultra-wideband positioning system,
such as Pozyx or NXP Rddrone.
- [SensorsStatus](SensorsStatus.md) — Sensor check metrics. This will be zero for a sensor that's primary or unpopulated.
- [SensorsStatusImu](SensorsStatusImu.md) — Sensor check metrics. This will be zero for a sensor that's primary or unpopulated.
- [SystemPower](SystemPower.md)
- [TakeoffStatus](TakeoffStatus.md) — Status of the takeoff state machine currently just available for multicopters
- [TaskStackInfo](TaskStackInfo.md) — stack information for a single running process
- [TecsStatus](TecsStatus.md)
- [TelemetryStatus](TelemetryStatus.md)
- [TiltrotorExtraControls](TiltrotorExtraControls.md)
- [TimesyncStatus](TimesyncStatus.md)
- [TrajectorySetpoint6dof](TrajectorySetpoint6dof.md) — Trajectory setpoint in NED frame
Input to position controller.
- [TransponderReport](TransponderReport.md)
- [TuneControl](TuneControl.md) — This message is used to control the tunes, when the tune_id is set to CUSTOM
then the frequency, duration are used otherwise those values are ignored.
- [UavcanParameterRequest](UavcanParameterRequest.md) — UAVCAN-MAVLink parameter bridge request type
- [UavcanParameterValue](UavcanParameterValue.md) — UAVCAN-MAVLink parameter bridge response type
- [UlogStream](UlogStream.md) — Message to stream ULog data from the logger. Corresponds to the LOGGING_DATA
mavlink message
- [UlogStreamAck](UlogStreamAck.md) — Ack a previously sent ulog_stream message that had
the NEED_ACK flag set
- [VehicleAcceleration](VehicleAcceleration.md)
- [VehicleAirData](VehicleAirData.md)
- [VehicleAngularAccelerationSetpoint](VehicleAngularAccelerationSetpoint.md)
- [VehicleConstraints](VehicleConstraints.md) — Local setpoint constraints in NED frame
setting something to NaN means that no limit is provided
- [VehicleImu](VehicleImu.md) — IMU readings in SI-unit form.
- [VehicleImuStatus](VehicleImuStatus.md)
- [VehicleLocalPositionSetpoint](VehicleLocalPositionSetpoint.md) — Local position setpoint in NED frame
Telemetry of PID position controller to monitor tracking.
NaN means the state was not controlled
- [VehicleMagnetometer](VehicleMagnetometer.md)
- [VehicleOpticalFlow](VehicleOpticalFlow.md) — Optical flow in XYZ body frame in SI units.
- [VehicleOpticalFlowVel](VehicleOpticalFlowVel.md)
- [VehicleRoi](VehicleRoi.md) — Vehicle Region Of Interest (ROI)
- [VehicleThrustSetpoint](VehicleThrustSetpoint.md)
- [VehicleTorqueSetpoint](VehicleTorqueSetpoint.md)
- [VelocityLimits](VelocityLimits.md) — Velocity and yaw rate limits for a multicopter position slow mode only
- [WheelEncoders](WheelEncoders.md)
- [Wind](Wind.md)
- [YawEstimatorStatus](YawEstimatorStatus.md)
- [AirspeedValidatedV0](AirspeedValidatedV0.md)
- [VehicleAttitudeSetpointV0](VehicleAttitudeSetpointV0.md)
- [VehicleStatusV0](VehicleStatusV0.md) — Encodes the system state of the vehicle published by commander