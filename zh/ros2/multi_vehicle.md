# 使用 ROS 2 的多机体模拟

[XRCE-DDS](../middleware/uxrce_dds.md) 允许多个客户端通过 UDP 连接到同一个代理。
这在模拟中特别有用，因为只需启动一个代理即可。

## 设置与要求

唯一的要求是：

- 能够在不使用 ROS 2 的情况下运行 [多机体模拟](../simulation/multi-vehicle-simulation.md)，所选模拟器包括 ([Gazebo](../sim_gazebo_gz/multi_vehicle_simulation.md), [Gazebo Classic](../sim_gazebo_classic/multi_vehicle_simulation.md#multiple-vehicle-with-gazebo-classic), [FlightGear](../sim_flightgear/multi_vehicle.md) 和 [JMAVSim](../sim_jmavsim/multi_vehicle.md))。
- 能够在单机体模拟中使用 [ROS 2](../ros2/user_guide.md)。

## 运行原理

在模拟中，每个 PX4 实例会接收到一个唯一的 `px4_instance` 编号（从 `0` 开始）。
此值用于分配一个唯一值给 [UXRCE_DDS_KEY](../advanced_config/parameter_reference.md#UXRCE_DDS_KEY)：

```sh
param set UXRCE_DDS_KEY $((px4_instance+1))
```

::: info
通过这种方式，`UXRCE_DDS_KEY` 将始终与 [MAV_SYS_ID](../advanced_config/parameter_reference.md#MAV_SYS_ID) 一致。
:::

此外，当 `px4_instance` 大于零时，会添加一个形式为 `px4_$px4_instance` 的唯一 ROS 2 [命名空间前缀](../middleware/uxrce_dds.md#customizing-the-namespace)：

```sh
uxrce_dds_ns="-n px4_$px4_instance"
```

::: info
如果设置了环境变量 `PX4_UXRCE_DDS_NS`，它将覆盖上述描述的命名空间行为。
:::

第一个实例 (`px4_instance=0`) 不包含额外的命名空间，以与真实机体上 `xrce-dds` 客户端的默认行为保持一致。
此差异可以通过手动在第一个实例中使用 `PX4_UXRCE_DDS_NS` 或从索引 `1` 而非 `0` 开始添加机体来修正（这是 [sitl_multiple_run.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/simulation/gazebo-classic/sitl_multiple_run.sh) 在 Gazebo Classic 中采用的默认行为）。

模拟中的默认客户端配置总结如下：

| `PX4_UXRCE_DDS_NS` | `px4_instance` | `UXRCE_DDS_KEY`   | 客户端命名空间      |
|-------------------|----------------|------------------|-----------------------|
| 未提供            | 0              | `px4_instance+1` | 无                  |
| 已提供            | 0              | `px4_instance+1` | `PX4_UXRCE_DDS_NS`     |
| 未提供            | >0             | `px4_instance+1` | `px4_${px4_instance}` |
| 已提供            | >0             | `px4_instance+1` | `PX4_UXRCE_DDS_NS`     |

## 调整 `target_system` 值

PX4 仅接受其 `target_system` 字段为零（广播通信）或与 `MAV_SYS_ID` 一致的 [VehicleCommand](../msg_docs/VehicleCommand.md) 消息。
在所有其他情况下，消息都会被忽略。
因此，当 ROS 2 节点需要向 PX4 发送 `VehicleCommand` 时，必须确保消息中包含适当的 `target_system` 值。

例如，如果要向第三个机体（`px4_instance=2`）发送命令，需要在所有 `VehicleCommand` 消息中设置 `target_system=3`。