# Vertiq 全集成电机/电调模块

Vertiq 为商用和军用无人机系统提供高性能推进系统。其核心设计采用轻量化的高集成度电机与电调组合，并嵌入位置传感器。通过闭环速度控制实现最快的响应时间，具有同类领先的效率，无启动和反向抖动从而实现低速控制和平滑反转，以及内置的"收起"控制器可将怠速螺旋桨平稳转向预设方向，这些特性使Vertiq模块相比其他电调具有显著优势。

![Vertiq Module Lineup](../../assets/peripherals/esc_vertiq/vertiq_esc_lineup.jpg)

所有Vertiq模块均支持传统的[PWM输入、DShot、OneShot和Multishot通信协议](https://iqmotion.readthedocs.io/en/latest/communication_protocols/hobby_protocol.html)。较大尺寸的Vertiq模块还支持[DroneCAN控制](https://iqmotion.readthedocs.io/en/latest/communication_protocols/dronecan_protocol.html)。

## 购买渠道

购买信息请访问[Vertiq官网](https://www.vertiq.co/)。

## 硬件配置

### 接线

将Vertiq模块连接到飞控的PWM输出或DroneCAN总线时，具体接线方式取决于模块型号。请参阅产品数据手册获取接线信息。

所有Vertiq数据手册均可在[vertiq.co](https://www.vertiq.co/)获取。

## 固件配置

配置Vertiq模块的最佳工具是Vertiq的IQ Control Center应用程序。安装指引请参考[使用IQ Control Center配置速度模块入门指南](https://iqmotion.readthedocs.io/en/latest/control_center_docs/speed_module_getting_started.html)。

如需使用PWM输入或DShot与飞控配合使用，请参阅[PWM和DShot与飞控的控制](https://iqmotion.readthedocs.io/en/latest/tutorials/pwm_control_flight_controller.html)。

如需使用DroneCAN与飞控配合使用，请参阅[DroneCAN与PX4飞控的集成](https://iqmotion.readthedocs.io/en/latest/tutorials/dronecan_flight_controller.html)。

## 飞控配置

### DroneCAN配置

通过DroneCAN集成电机/电调的配置说明请参阅[飞控配置](https://iqmotion.readthedocs.io/en/latest/tutorials/dronecan_flight_controller.html#dronecan-integration-with-a-px4-flight-controller)（在_DroneCAN与PX4飞控集成_章节中）。

该指南将引导您设置飞控的DroneCAN驱动参数，配置DroneCAN总线与Vertiq模块的通信参数，进行电调配置，并测试飞控能否通过DroneCAN正确控制模块。

### DShot/PWM配置

通过PWM和DShot集成电机/电调的配置说明请参阅[PWM和DShot与飞控的控制](https://iqmotion.readthedocs.io/en/latest/tutorials/pwm_control_flight_controller.html)。
推荐使用DShot协议。

## 更多信息

- <https://www.vertiq.co/> — 了解Vertiq模块详情
- [Vertiq 文档](https://iqmotion.readthedocs.io/en/latest/index.html) — Vertiq模块配置附加信息