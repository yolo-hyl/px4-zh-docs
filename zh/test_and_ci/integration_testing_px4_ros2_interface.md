# PX4 ROS 2接口库的集成测试

本主题概述了[PX4 ROS 2接口库](../ros2/px4_ros2_interface_lib.md)的集成测试。

这些测试验证模式注册、安全保护和模式替换是否按预期工作。

## CI测试

向PX4提交拉取请求时，CI会运行库的集成测试。

## 本地运行测试

测试也可以从PX4本地运行：

```sh
./test/ros_test_runner.py
```

运行单个测试用例：

```sh
./test/ros_test_runner.py --verbose --case <case>
```

您可以使用以下命令列出可用的测试用例：

```sh
./test/ros_test_runner.py --list-cases
```