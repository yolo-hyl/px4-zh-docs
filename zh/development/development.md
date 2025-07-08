# PX4 开发

本节解释如何支持新的机体类型和变体、修改飞行算法、添加新飞行模式、集成新硬件，以及如何从飞行控制器外部与PX4通信。

:::tip
本节面向软件开发者和（新）硬件集成者。
如果您是在构建现有机体或使用PX4机体飞行，则无需阅读本节。
:::

它解释了如何：

- 获取[最小开发环境](../dev_setup/config_initial.md)，[从源码构建PX4](../dev_setup/building_px4.md)并部署到[多种支持的飞控硬件](../flight_controller/index.md)。
- 理解[PX4系统架构](../concept/architecture.md)及其他核心概念。
- 学习如何修改飞行栈和中间件：
  - 修改飞行算法并添加新[飞行模式](../concept/flight_modes.md)。
  - 支持新的[机体类型](../dev_airframes/index.md)。
- 学习如何将PX4与新硬件集成：
  - 支持新传感器和执行器（包括摄像头、测距仪等）。
  - 修改PX4以在新飞控硬件上运行。
- [仿真](../simulation/index.md)、[测试](../test_and_ci/index.md)和[调试/日志](../debug/index.md)PX4。
- 与外部机器人API通信/集成。

## 关键开发者链接

- [支持](../contribute/support.md)：通过[讨论论坛](https://discuss.px4.io//)及其他支持渠道获取帮助。
- [每周开发会议](../contribute/dev_call.md)：这是与PX4开发团队会面并讨论平台技术细节的绝佳机会（包括拉取请求、主要问题、通用问答）。
- [许可证](../contribute/licenses.md)：您可以如何使用代码（代码可在[BSD 3-clause license](https://opensource.org/licenses/BSD-3-Clause)许可下自由使用和修改！）
- [贡献](../contribute/index.md)：如何与我们的[源代码](../contribute/code.md)协作。