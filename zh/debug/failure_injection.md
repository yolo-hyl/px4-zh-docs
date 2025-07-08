# 系统故障注入

系统故障注入允许通过[MAVSDK failure plugin](https://mavsdk.mavlink.io/main/en/cpp/api_reference/classmavsdk_1_1_failure.html)以编程方式或通过PX4控制台（如[MAVLink shell](../debug/mavlink_shell.md#mavlink-shell)）"手动"模拟各种传感器和系统故障。这使得更容易测试[safety failsafe](../config/safety.md)行为，以及更广泛地测试PX4在系统和传感器停止正常工作时的表现。

故障注入默认禁用，可通过[SYS_FAILURE_EN](../advanced_config/parameter_reference.md#SYS_FAILURE_EN)参数启用。

:::warning
故障注入功能仍在开发中。
截至写作时（PX4 v1.14）：

- 仅能在仿真环境中使用（真实飞行中的故障注入支持正在计划中）
- 需要仿真器支持。Gazebo Classic已支持
- 许多故障类型尚未广泛实现，这些情况下命令会返回"unsupported"消息

:::

## 故障系统命令

可通过任何PX4控制台/shell使用[failure系统命令](../modules/modules_command.md#failure)注入故障，指定目标和故障类型。

### 语法

[failure](../modules/modules_command.md#failure)命令的完整语法为：

```sh
failure <component> <failure_type> [-i <instance_number>]
```

其中：

- _component_:
  - 传感器：
    - `gyro`：陀螺仪
    - `accel`：加速度计
    - `mag`：磁力计
    - `baro`：气压计
    - `gps`：GPS
    - `optical_flow`：光流
    - `vio`：视觉惯性里程计
    - `distance_sensor`：距离传感器（测距仪）
    - `airspeed`：空速传感器
  - 系统：
    - `battery`：电池
    - `motor`：电机
    - `servo`：舵机
    - `avoidance`：避障
    - `rc_signal`：遥控信号
    - `mavlink_signal`：MAVLink信号（数据遥测）
- _failure_type_:
  - `ok`：正常发布（禁用故障注入）
  - `off`：停止发布
  - `stuck`：每次报告相同值（可能表示传感器故障）
  - `garbage`：发布随机噪声（类似读取未初始化内存）
  - `wrong`：发布无效值（但仍显得合理/不为"garbage"）
  - `slow`：以降低的频率发布
  - `delayed`：以显著延迟发布有效数据
  - `intermittent`：间歇性发布
- _instance number_（可选）：受影响传感器的实例号。0（默认）表示所有指定类型的传感器。

### 示例

无需关闭遥控器即可模拟丢失遥控信号：

1. 启用参数[SYS_FAILURE_EN](../advanced_config/parameter_reference.md#SYS_FAILURE_EN)
1. 在MAVLink控制台或SITL的_pxh shell_中输入以下命令：

   ```sh
   # 故障RC（关闭发布）
   failure rc_signal off

   # 重新启动RC发布
   failure rc_signal ok
   ```

## MAVSDK 故障插件

[MAVSDK failure plugin](https://mavsdk.mavlink.io/main/en/cpp/api_reference/classmavsdk_1_1_failure.html)可用于编程方式注入故障。该插件在[PX4 Integration Testing](../test_and_ci/integration_testing_mavsdk.md)中用于模拟故障场景（例如参见[PX4-Autopilot/test/mavsdk_tests/autopilot_tester.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/test/mavsdk_tests/autopilot_tester.cpp)）。

插件API是上述故障命令的直接映射，包含一些与连接相关的额外错误信号。