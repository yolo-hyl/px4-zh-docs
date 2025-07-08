# 飞行控制器移植指南

本主题面向希望将PX4移植到**新型**飞行控制器硬件的开发者。

## PX4架构

PX4由两层主要架构组成：在宿主操作系统（NuttX、Linux或任何POSIX平台如Mac OS）之上的[板级支持和中间件层](../middleware/index.md)，以及应用层（飞行栈在[src/modules](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules)中）。更多信息请参考[PX4架构概览](../concept/architecture.md)。

本指南仅关注宿主操作系统和中间件，因为应用程序/飞行栈将在任何板级目标上运行。

## 飞行控制器配置文件布局

板级启动和配置文件位于每个板级厂商特定目录下的[/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards/)路径中（即**boards/_VENDOR_/_MODEL_/**）。

以FMUv5为例：
- (所有) 板级特定文件：[/boards/px4/fmu-v5](https://github.com/PX4/PX4-Autopilot/tree/main/boards/px4/fmu-v5).<!-- NEED px4_version -->
- 构建配置：[/boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board).<!-- NEED px4_version -->
- 板级特定初始化文件：[/boards/px4/fmu-v5/init/rc.board_defaults](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/init/rc.board_defaults) <!-- NEED px4_version -->
  - 如果在**init/rc.board**路径下找到板级特定初始化文件，该文件会自动包含在启动脚本中。
  - 该文件用于启动仅存在于特定板级的传感器（及其他组件），也可用于设置板级默认参数、UART映射及特殊案例。
  - 对于FMUv5，可以看到所有Pixhawk 4传感器的启动，并设置了更大的LOGGER_BUF。

## 宿主操作系统配置

本节描述移植新飞行控制器硬件所需配置文件的用途和位置。

### NuttX

参见[NuttX板级移植指南](porting_guide_nuttx.md)。

### Linux

Linux板级不包含操作系统和内核配置。这些由板级可用的Linux镜像提供（需要开箱即用支持惯性传感器）。

- [boards/px4/raspberrypi/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/raspberrypi/default.px4board) - RPi交叉编译。 <!-- NEED px4_version -->

## 中间件组件和配置

本节描述各种中间件组件，以及移植到新飞行控制器硬件所需的配置文件更新。

### QuRT / Hexagon

- 启动脚本位于[posix-configs/](https://github.com/PX4/PX4-Autopilot/tree/main/posix-configs)。 <!-- NEED px4_version -->
- 操作系统配置是默认Linux镜像的一部分（TODO: 提供LINUX IMAGE位置和烧录说明）。
- PX4中间件配置位于[src/boards](https://github.com/PX4/PX4-Autopilot/tree/main/boards)。 <!-- NEED px4_version --> TODO: 添加总线配置

## RC UART接线建议

建议通过微控制器的独立RX和TX引脚连接遥控器。如果RX和TX连接在一起，UART必须切换为单线模式以避免冲突。这需要通过板级配置和清单文件实现。例如[px4fmu-v5](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/src/manifest.c)。 <!-- NEED px4_version -->

## 官方支持的硬件

PX4项目支持并维护[FMU标准参考硬件](../hardware/reference_design.md)以及与标准兼容的任何板级。这包括[Pixhawk系列](../flight_controller/pixhawk_series.md)（详见用户指南的[官方支持硬件完整列表](../flight_controller/index.md)）。

每个官方支持的板级均享有：
- PX4移植版本在PX4仓库中提供
- 可通过_QGroundControl_访问的自动固件构建
- 与生态系统其余部分的兼容性
- 通过CI的自动检查-安全始终是社区的首要任务
- [飞行测试](../test_and_ci/test_flights.md)

我们鼓励板级制造商以[FMU规范](https://pixhawk.org/)为目标实现完全兼容。通过完全兼容，您可以受益于PX4的持续开发，而无需承担支持偏离规范带来的维护成本。

:::tip
制造商在偏离规范前应仔细考虑维护成本（制造商承担的成本与偏离程度成正比）。
:::

我们欢迎任何个人或公司提交移植版本以包含在我们的支持硬件中，前提是愿意遵守我们的[行为准则](https://github.com/PX4/PX4-Autopilot/blob/main/CODE_OF_CONDUCT.md)并与开发团队合作，为客户提供安全且令人满意的PX4体验。

需要特别说明的是，PX4开发团队有责任发布安全软件，因此我们要求任何板级制造商投入必要资源保持其移植版本的更新和可用状态。

如果您希望在PX4中获得官方支持：
- 您的硬件必须在市场上可用（即任何开发者均可不受限制购买）
- 硬件必须提供给PX4开发团队以验证移植（联系[lorenz@px4.io](mailto:lorenz@px4.io)获取测试硬件寄送指导）
- 板级必须通过完整的[测试套件](../test_and_ci/test_flights.md)和[飞行测试](../test_and_ci/test_flights.md)

**PX4项目保留最终决定权**。若不符合上述要求，PX4项目保留拒绝支持的权利。

## 相关资源

- [设备兼容性列表](https://docs.px4.io/main/en/boards/)
- [硬件开发指南](https://github.com/PX4/PX4-Autopilot/tree/main/boards)
- [PX4开发套件](https://store.px4.io/)