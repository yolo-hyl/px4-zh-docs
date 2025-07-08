# 集成测试

集成测试用于验证系统中较大组件协同工作的效果。  
在PX4中，这通常意味着测试机体的完整功能，通常在仿真环境中运行。  
所有测试均在 [持续集成 (CI)](../test_and_ci/continous_integration.md) 中执行，覆盖每次拉取请求。

- [MAVSDK集成测试](../test_and_ci/integration_testing_mavsdk.md) - 基于MAVSDK的测试框架，用于PX4。  
  _这是编写新集成测试的推荐框架_
- [PX4 ROS2 Interface Library集成测试](../test_and_ci/integration_testing_px4_ros2_interface.md) - [PX4 ROS 2 Interface Library](../ros2/px4_ros2_interface_lib.md) 的集成测试。

以下框架仅应用于需要ROS 1的测试：

- [ROS 1集成测试](../test_and_ci/integration_testing_ros1_mavros.md) (已弃用)