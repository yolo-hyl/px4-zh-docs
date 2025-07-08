# 使用Gazebo的多机体仿真

本主题说明如何使用 [Gazebo (Gz)](../sim_gazebo_gz/index.md) 和 SITL 模拟多个UAV机体。

::: info
使用Gazebo的多机体仿真仅支持Linux系统。
:::

Gazebo使得设置多机体场景变得非常简单（相比其他模拟器）。

首先使用以下命令构建PX4 SITL代码：

```sh
make px4_sitl
```

然后可以在各自的终端中启动每个PX4实例，指定一个唯一的实例编号及其所需的[环境变量](../sim_gazebo_gz/index.md#usage-configuration-options)组合：

```sh
ARGS ./build/px4_sitl_default/bin/px4 [-i <instance>]
```

- `<instance>`:
  - 机体的实例编号。
  - 每个机体必须具有唯一的实例编号。如未指定，默认值为零。
  - 当与 `PX4_SIM_MODEL` 一起使用时，Gazebo将自动选择一个唯一模型名称，格式为 `${PX4_SIM_MODEL}_instance`。
- `ARGS`:
  - 环境变量列表，如 [Gazebo Simulation > Usage/Configuration Options](../sim_gazebo_gz/index.md#usage-configuration-options) 中所述。

这为场景配置提供了更大的灵活性和定制化选项。

## 使用ROS 2和Gazebo的多机体

[使用ROS 2的多机体](../ros2/multi_vehicle.md) 是可行的。

- 首先按照 [Gazebo](../sim_gazebo_gz/index.md) 的安装说明进行操作。
- 然后为 [ROS 2 / PX4 操作](../ros2/user_guide.md#installation-setup) 配置系统。
- 在不同终端中手动启动多机体仿真。该示例将生成2个X500四旋翼和1个FPX固定翼。

  ::: info
  注意在第一个终端中你**不需要**指定独立模式。第一个终端将启动gz-server，其他两个实例将连接到它。
  :::

  **终端1**

  ```sh
  PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 1
  ```

  **终端2**

  ```sh
  PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4001 PX4_GZ_MODEL_POSE="0,1" PX4_SIM_MODEL=gz_x500 ./build/px4_sitl_default/bin/px4 -i 2
  ```

  **终端3**

  ```sh
  PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4003 PX4_GZ_MODEL_POSE="0,2" PX4_SIM_MODEL=gz_rc_cessna ./build/px4_sitl_default/bin/px4 -i 3
  ```

- 启动代理：

  ```sh
  MicroXRCEAgent udp4 -p 8888
  ```

  代理将自动连接所有客户端。

- 运行 `ros2 topic list` 查看主题列表，应该如下所示：

```sh
/parameter_events
/px4_1/fmu/in/obstacle_distance
/px4_1/fmu/in/offboard_control_mode
/px4_1/fmu/in/onboard_computer_status
/px4_1/fmu/in/sensor_optical_flow
/px4_1/fmu/in/telemetry_status
/px4_1/fmu/in/trajectory_setpoint
/px4_1/fmu/in/vehicle_attitude_setpoint
/px4_1/fmu/in/vehicle_command
/px4_1/fmu/in/vehicle_mocap_odometry
/px4_1/fmu/in/vehicle_rates_setpoint
/px4_1/fmu/in/vehicle_trajectory_bezier
/px4_1/fmu/in/vehicle_trajectory_waypoint
/px4_1/fmu/in/vehicle_visual_odometry
/px4_1/fmu/out/failsafe_flags
/px4_1/fmu/out/sensor_combined
/px4_1/fmu/out/timesync_status
/px4_1/fmu/out/vehicle_attitude
/px4_1/fmu/out/vehicle_control_mode
/px4_1/fmu/out/vehicle_global_position
/px4_1/fmu/out/vehicle_gps_position
/px4_1/fmu/out/vehicle_local_position
/px4_1/fmu/out/vehicle_odometry
/px4_1/fmu/out/vehicle_status
/px4_2/fmu/in/obstacle_distance
/px4_2/fmu/in/offboard_control_mode
/px4_2/fmu/in/onboard_computer_status
/px4_2/fmu/in/sensor_optical_flow
/px4_2/fmu/in/telemetry_status
/px4_2/fmu/in/trajectory_setpoint
/px4_2/fmu/in/vehicle_attitude_setpoint
/px4_2/fmu/in/vehicle_command
/px4_2/fmu/in/vehicle_mocap_odometry
/px4_2/fmu/in/vehicle_rates_setpoint
/px4_2/fmu/in/vehicle_trajectory_bezier
/px4_2/fmu/in/vehicle_trajectory_waypoint
/px4_2/fmu/in/vehicle_visual_odometry
/px4_2/fmu/out/failsafe_flags
/px4_2/fmu/out/sensor_combined
/px4_2/fmu/out/timesync_status
/px4_2/fmu/out/vehicle_attitude
/px4_2/fmu/out/vehicle_control_mode
/px4_2/fmu/out/vehicle_global_position
/px4_2/fmu/out/vehicle_gps_position
/px4_2/fmu/out/vehicle_local_position
/px4_2/fmu/out/vehicle_odometry
/px4_2/fmu/out/vehicle_status
/px4_3/fmu/in/obstacle_distance
/px4_3/fmu/in/offboard_control_mode
/px4_3/fmu/in/onboard_computer_status
/px4_3/fmu/in/sensor_optical_flow
/px4_3/fmu/in/telemetry_status
/px4_3/fmu/in/trajectory_setpoint
/px4_3/fmu/in/vehicle_attitude_setpoint
/px4_3/fmu/in/vehicle_command
/px4_3/fmu/in/vehicle_mocap_odometry
/px4_3/fmu/in/vehicle_rates_setpoint
/px4_3/fmu/in/vehicle_trajectory_bezier
/px4_3/fmu/in/vehicle_trajectory_waypoint
/px4_3/fmu/in/vehicle_visual_odometry
/px4_3/fmu/out/failsafe_flags
/px4_3/fmu/out/sensor_combined
/px4_3/fmu/out/timesync_status
/px4_3/fmu/out/vehicle_attitude
/px4_3/fmu/out/vehicle_control_mode
/px4_3/fmu/out/vehicle_global_position
/px4_3/fmu/out/vehicle_gps_position
/px4_3/fmu/out/vehicle_local_position
/px4_3/fmu/out/vehicle_odometry
/px4_3/fmu/out/vehicle_status
/rosout
/rosout_agg
```