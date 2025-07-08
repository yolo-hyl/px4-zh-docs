# PX4 ROS 2 导航接口

<Badge type="tip" text="PX4 v1.15" /> <Badge type="warning" text="Experimental" />

:::warning Experimental
在撰写本文时，PX4 ROS 2 接口库的部分功能处于实验性阶段，因此可能会发生变化。
:::

[PX4 ROS 2 接口库](../ros2/px4_ros2_interface_lib.md)的导航接口允许开发者通过ROS 2应用程序（如VIO系统或地图匹配系统）直接向PX4发送位置测量数据。
该接口提供了从PX4和uORB消息框架的抽象层，并对接口发送的请求状态估计更新实施了一些健全性检查。
这些测量数据随后会与EKF融合，如同它们是PX4的内部测量数据一样。

该库提供了两个类：[`LocalPositionMeasurementInterface`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1LocalPositionMeasurementInterface.html)和[`GlobalPositionMeasurementInterface`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1GlobalPositionMeasurementInterface.html)，它们都暴露了类似的`update`方法，分别用于向PX4提供本地位置或全局位置更新。
`update`方法需要一个位置测量`struct`（[`LocalPositionMeasurement`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1LocalPositionMeasurement.html)或[`GlobalPositionMeasurement`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1GlobalPositionMeasurement.html)），开发者可以使用自己生成的位置测量数据填充这些结构体。

## 安装和首次测试

以下是开始使用所需的步骤：

1. 请确保已安装可用的 [ROS 2 环境](../ros2/user_guide.md)，并在 ROS 2 工作空间中包含 [`px4_msgs`](https://github.com/PX4/px4_msgs)。

2. 将仓库克隆到工作空间中：

   ```sh
   cd $ros_workspace/src
   git clone --recursive https://github.com/Auterion/px4-ros2-interface-lib
   ```

   ::: info
   为确保兼容性，请使用 PX4、_px4_msgs_ 和该库的最新 _main_ 分支。
   详见 [此处](https://github.com/Auterion/px4-ros2-interface-lib#compatibility-with-px4)。
   :::

3. 构建工作空间：

   ```sh
   cd ..
   colcon build
   ```

4. 在另一个终端中启动 PX4 SITL：

   ```sh
   cd $px4-autopilot
   make px4_sitl gazebo-classic
   ```

   （此处使用 Gazebo-Classic，但也可以使用其他模型或模拟器）

5. 再开一个终端，运行 micro XRCE 代理（后续可以保持运行）：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

6. 返回 ROS 2 终端，源代码刚刚构建的工作空间（第 3 步）并运行 [global_navigation](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/examples/cpp/navigation/global_navigation) 示例，该示例会定期发送虚拟全局位置更新：

   ```sh
   source install/setup.bash
   ros2 run example_global_navigation_cpp example_global_navigation
   ```

   你应该会看到类似以下输出，显示全局接口已成功发送位置更新：

   ```sh
   [INFO] [1702030701.836897756] [example_global_navigation_node]: example_global_navigation_node running!
   [DEBUG] [1702030702.837279784] [example_global_navigation_node]: Successfully sent position update to navigation interface.
   [DEBUG] [1702030703.837223884] [example_global_navigation_node]: Successfully sent position update to navigation interface.
   ```

7. 在 PX4 终端中，可以检查 PX4 是否接收到全局位置更新：

   ```sh
   listener aux_global_position
   ```

   输出应类似：

   ```sh
   TOPIC: aux_global_position
   aux_global_position
      timestamp: 46916000 (0.528000 seconds ago)
      timestamp_sample: 46916000 (0 us before timestamp)
      lat: 12.343210
      lon: 23.454320
      alt: 12.40000
      alt_ellipsoid: 0.00000
      delta_alt: 0.00000
      eph: 0.31623
      epv: 0.44721
      terrain_alt: 0.00000
      lat_lon_reset_counter: 0
      alt_reset_counter: 0
      terrain_alt_valid: False
      dead_reckoning: False
   ```

8. 现在你可以使用导航接口发送自己的位置更新了。

## 如何使用该库

要发送位置测量数据，首先用你测量的值填充位置结构体，然后用该结构体作为参数调用接口的更新函数。

要查看该接口的基本使用示例，请查阅 `Auterion/px4-ros2-interface-lib` 仓库中的[示例](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/examples/cpp/navigation)，例如 [examples/cpp/navigation/local_navigation](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/examples/cpp/navigation/local_navigation/include/local_navigation.hpp) 或 [examples/cpp/navigation/global_navigation](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/examples/cpp/navigation/local_navigation/include/global_navigation.hpp)。

### 本地位置更新

首先确保 PX4 参数 [`EKF2_EV_CTRL`](../advanced_config/parameter_reference.md#EKF2_EV_CTRL) 正确配置以融合外部本地测量数据，通过设置相应位为 `true`：

- `0`: 水平位置数据
- `1`: 垂直位置数据
- `2`: 速度数据
- `3`: 偏航角数据

要向 PX4 发送本地位置测量数据：

1. 通过提供一个 ROS 节点以及测量的姿态和速度参考帧，创建一个 [`LocalPositionMeasurementInterface`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1LocalPositionMeasurementInterface.html) 实例。
2. 用你的测量数据填充一个 [`LocalPositionMeasurement`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1LocalPositionMeasurement.html) 结构体。
3. 将该结构体传递给 `LocalPositionMeasurementInterface` 的 [`update()`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1LocalPositionMeasurementInterface.html#a6fd180b944710716d418b2cfe1c0c8e3) 方法。

你的测量数据可用的姿态和速度参考帧由以下 `enum` 定义：

```cpp
enum class PoseFrame
{
  Unknown,
  LocalNED,
  LocalFRD
};

enum class VelocityFrame
{
  Unknown,
  LocalNED,
  LocalFRD,
  BodyFRD
};
```

`LocalPositionMeasurement` 结构体定义如下：

```cpp
struct LocalPositionMeasurement
{
   rclcpp::Time timestamp_sample {};

   std::optional<Eigen::Vector2f> position_xy {std::nullopt};
   std::optional<Eigen::Vector2f> position_xy_variance {std::nullopt};
   std::optional<float> position_z {std::nullopt};
   std::optional<float> position_z_variance {std::nullopt};

   std::optional<Eigen::Vector2f> velocity_xy {std::nullopt};
   std::optional<Eigen::Vector2f> velocity_xy_variance {std::nullopt};
   std::optional<float> velocity_z {std::nullopt};
   std::optional<float> velocity_z_variance {std::nullopt};

   std::optional<Eigen::Quaternionf> attitude_quaternion {std::nullopt};
   std::optional<Eigen::Vector3f> attitude_variance {std::nullopt};
};
```

本地接口的 `update()` 方法对 `LocalPositionMeasurement` 有以下要求：

- 样本时间戳必须已定义。
- 值不能为 `NAN`。
- 如果提供了测量值，其相关方差值必须已定义（例如，如果 `position_xy` 已定义，则 `position_xy_variance` 必须已定义）。
- 如果提供了测量值，其相关参考帧不能为未知（例如，如果 `position_xy` 已定义，则接口初始化时的姿态参考帧必须不是 `PoseFrame::Unknown`）。

以下代码片段是一个 ROS 2 节点示例，使用本地导航接口以北-东-下（NED）参考帧向 PX4 发送 3D 姿态更新：

```cpp
class MyLocalMeasurementUpdateNode : public rclcpp::Node
{
public:
   MyLocalMeasurementUpdateNode()
   : Node("my_node_name")
   {
      // 将姿态测量参考帧设置为北-东-下
      const px4_ros2::PoseFrame pose_frame = px4_ros2::PoseFrame::LocalNED;
      // 在本示例中，我们仅发送姿态测量数据
      // 将速度测量参考帧设置为未知
      const px4_ros2::VelocityFrame velocity_frame = px4_ros2::VelocityFrame::Unknown;
      // 初始化本地接口 [1]
      _local_position_measurement_interface =
         std::make_shared<px4_ros2::LocalPositionMeasurementInterface>(*this, pose_frame, velocity_frame);
   }

   void sendUpdate()
   {
      while (running) { // 可能作为回调或定时器运行方法
         // 生成本地位置测量数据
         rclcpp::Time timestamp_sample  = ...
         Eigen::Vector2f position_xy = ...
         Eigen::Vector2f position_xy_variance = ...
         float position_z = ...
         float position_z_variance = ...

         // 填充本地位置测量结构体 [2]
         px4_ros2::LocalPositionMeasurement local_position_measurement{};
         local_position_measurement.timestamp_sample = timestamp_sample;
         local_position_measurement.position_xy = position_xy;
         local_position_measurement.position_xy_variance = position_xy_variance;
         local_position_measurement.position_z = position_z;
         local_position_measurement.position_z_variance = position_z_variance;

         // 使用接口向 PX4 发送测量数据 [3]
         try {
            _local_position_measurement_interface->update(local_position_measurement);
         } catch (const px4_ros2::NavigationInterfaceInvalidArgument & e) {
            // 处理因无效本地位置测量定义引发的异常
            RCLCPP_ERROR(get_logger(), "Exception caught: %s", e.what());
         }
      }
   }

private:
   std::shared_ptr<px4_ros2::LocalPositionMeasurementInterface> _local_position_measurement_interface;
};
```

### 全球位置更新

首先确保 PX4 参数 [`EKF2_AGP_CTRL`](../advanced_config/parameter_reference.md#EKF2_AGP_CTRL) 正确配置以融合外部全球测量数据，通过设置相应位为 `true`：

- `0`: 水平位置数据
- `1`: 垂直位置数据

要向 PX4 发送全球位置测量数据：

1. 通过提供一个 ROS 节点，创建一个 [`GlobalPositionMeasurementInterface`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1GlobalPositionMeasurementInterface.html) 实例。
2. 用你的测量数据填充一个 [`GlobalPositionMeasurement`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1GlobalPositionMeasurement.html) 结构体。
3. 将该结构体传递给 `GlobalPositionMeasurementInterface` 的 [`update()`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1GlobalPositionMeasurementInterface.html#a6fd180b944710716d418b2cfe1c0c8e3) 方法。

你的测量数据可用的姿态和速度参考帧由以下 `enum` 定义：

```cpp
enum class GlobalFrame
{
  WGS84,
  UTM
};
```

`GlobalPositionMeasurement` 结构体定义如下：

```cpp
struct GlobalPositionMeasurement
{
   rclcpp::Time timestamp_sample {};

   Eigen::Vector2d lat_lon;
   float horizontal_variance;
   float altitude_msl;
   float vertical_variance;
};
```

全球接口的 `update()` 方法对 `GlobalPositionMeasurement` 有以下要求：

- 样本时间戳必须已定义。
- 值不能为 `NAN`。
- 如果提供了测量值，其相关方差值必须已定义（例如，如果 `lat_lon` 已定义，则 `horizontal_variance` 必须已定义）。
- 如果提供了测量值，其相关参考帧不能为未知（例如，如果 `lat_lon` 已定义，则接口初始化时的全球参考帧必须不是 `GlobalFrame::Unknown`）。

以下代码片段是一个 ROS 2 节点示例，使用全球导航接口向 PX4 发送全球位置更新：

```cpp
class MyGlobalMeasurementUpdateNode : public rclcpp::Node
{
public:
   MyGlobalMeasurementUpdateNode()
   : Node("my_node_name")
   {
      // 初始化全球接口 [1]
      _global_position_measurement_interface =
         std::make_shared<px4_ros2::GlobalPositionMeasurementInterface>(*this);
   }

   void sendUpdate()
   {
      while (running) { // 可能作为回调或定时器运行方法
         // 生成全球位置测量数据
         rclcpp::Time timestamp_sample  = ...
         Eigen::Vector2d lat_lon = ...
         float horizontal_variance = ...
         float altitude_msl = ...
         float vertical_variance = ...

         // 填充全球位置测量结构体 [2]
         px4_ros2::GlobalPositionMeasurement global_position_measurement{};
         global_position_measurement.timestamp_sample = timestamp_sample;
         global_position_measurement.lat_lon = lat_lon;
         global_position_measurement.horizontal_variance = horizontal_variance;
         global_position_measurement.altitude_msl = altitude_msl;
         global_position_measurement.vertical_variance = vertical_variance;

         // 使用接口向 PX4 发送测量数据 [3]
         try {
            _global_position_measurement_interface->update(global_position_measurement);
         } catch (const px4_ros2::NavigationInterfaceInvalidArgument & e) {
            // 处理因无效全球位置测量定义引发的异常
            RCLCPP_ERROR(get_logger(), "Exception caught: %s", e.what());
         }
      }
   }

private:
   std::shared_ptr<px4_ros2::GlobalPositionMeasurementInterface> _global_position_measurement_interface;
};
```

## 接口的多个实例

使用同一接口的多个实例（例如 local 和 local）发送估算更新时，所有更新消息将被发送到相同的话题，从而导致串扰。  
这不会影响测量数据融合到扩展卡尔曼滤波器（EKF）中，但不同测量源将变得无法区分。