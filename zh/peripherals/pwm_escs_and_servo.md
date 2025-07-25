# PWM舵机和电调（电机控制器）

本节描述如何连接和供电基于PWM的无刷电机控制器和舵机。

## 电调连接概述

每个PWM电子调速器（ESC）至少包含以下线缆：

- 电池电压VBAT（通常为粗线且红色）
- 电源接地GND（通常为粗线且黑色）

在舵机插头中：

- PWM信号（通常为白色或黄色）
- 接地GND（通常为黑色或棕色）

舵机插头_可能_还包含+5V线（通常为红色或橙色）。
该线缆的用途及其连接方式取决于特定的ESC和机体类型。

:::tip
在某些情况下（见下文），+5V线路可能不需要连接。
您可以轻轻抬起舵机连接器塑料外壳上对应针脚的锁定卡扣（例如使用裁纸刀片或小型螺丝刀），然后拔出该针脚。
用绝缘胶带进行隔离并将其固定在舵机电缆上。
这允许您在需要时轻松移除该线路。
:::

## 电源连接

始终将电源VBAT和GND连接到电池，并将舵机插头的PWM信号和GND连接到电机。

:::tip
**不存在无需信号地线的配置**！
:::

+5V线（如果存在）的连接方式取决于ESC/机体类型。

### 固定翼/垂直起降

在固定翼（或VTOL）电调上，+5V线路通常提供电池消除电路（BEC）的输出：

- 可以连接到Pixhawk舵机供电轨，用于为襟翼、副翼等舵机供电。

  ::: info
  从自动驾驶仪的航空电子电源供电给舵机或电调是不安全的。
  这就是为什么**Pixhawk系列**飞控不为舵机供电轨供电（AUX舵机供电轨未供电且限制在1A）。
  :::

- 通常建议仅将**一个BEC的输出**连接到Pixhawk舵机供电轨。
  （虽然可能连接多个+5V输出到供电轨，但具体取决于电调型号）。

### 多旋翼

在多旋翼上，+5V线路可能不存在，或即使存在也可能未连接。

- 多旋翼通常不需要舵机，因此不需要为Pixhawk舵机供电轨供电（电机通常通过配电板单独供电）。
- 将该线路连接到舵机供电轨不会造成任何危害（或益处）。
- 大疆电调通常包含此线路，但未连接。

### 光隔离电调

在无BEC的光隔离电调上，+5V线路可能需要连接并供电（以向电调微控制器提供电源）。
在这种情况下，线路通常连接到飞控舵机供电轨，且舵机供电轨必须通过额外的BEC供电。

## PX4配置

PWM电机和舵机通过QGroundControl中的[执行器配置](../config/actuators.md)界面进行配置。

在分配输出和基本校准后，您可能需要执行[电调校准](../advanced_config/esc_calibration.md)。

其他PX4 PWM配置参数可在此处找到：[PWM输出](../advanced_config/parameter_reference.md#pwm-outputs)。

## 故障排除

Pixhawk兼容市场上所有_PWM电调_。
如果特定电调无法正常工作，可能是接线或配置有误。

### 地线连接

检查电调舵机连接器的接地线（黑色线）是否连接到Pixhawk（不存在不需要地线参考的正确接线方式）。

:::warning
未连接地线飞行是不安全的。
这是因为每个正脉冲（电调信号）都需要相邻的地线回路以确保信号形状的清洁。

下图显示了未连接GND时信号变得嘈杂的情况。

![无地线的PWM](../../assets/hardware/pwm_esc/pwm_without_gnd.png)
:::

### 电源连接/光隔离电调

如果使用不提供BEC/电源输出的光隔离电调，请确保电调的+5V线路是否需要为光隔离器供电。

请参见本页面的第一部分了解其他电源连接注意事项。

### 无效的最小值

某些电调需要在启动前看到特殊的低值脉冲（以保护用户在电源开启时油门杆处于中间位置）。

PX4在机体解除武装时发送脉冲，这会静音电调并确保电调正确初始化。
合适的值在[执行器配置/测试](../config/actuators.md#actuator-testing)过程中确定并设置（内部对应输出参数[PWM_MAIN_DISn](../advanced_config/parameter_reference.md#PWM_MAIN_DIS1)和[PWM_AUX_DISn](../advanced_config/parameter_reference.md#PWM_AUX_DIS1)）。

### 超时

某些电调可能在电源开启后几秒内未接收到有效低脉冲时超时（阻止电机激活）。

PX4在电源开启后立即发送空闲/解除武装脉冲以防止电调超时。
合适的值在[执行器配置/测试](../config/actuators.md#actuator-testing)过程中确定并设置（内部对应输出参数[PWM_MAIN_DISn](../advanced_config/parameter_reference.md#PWM_MAIN_DIS1)和[PWM_AUX_DISn](../advanced_config/parameter_reference.md#PWM_AUX_DIS1)）。

### 有效的脉冲形状、电压和更新率

::: info
这通常不会出现问题，但为完整性列出
:::

Pixhawk使用主动高电平脉冲，这是主要品牌（如Futaba、Spektrum、FrSky）使用的标准。

PWM接口没有正式标准，但所有正常微控制器都使用TTL或CMOS电压电平。
TTL定义为低<0.8V且高>2.0V，部分制造商使用>2.4V以增加抗噪余量。
CMOS逻辑定义为相似的电压电平。
5V电平**从来不需要**成功切换到_开启_状态。

:::tip
Futaba、FrSky和Spektrum接收器输出3.3V或3.0V电压电平，因为它们远高于2.4V。
Pixhawk已采用此行业通用模式，并在最新版电路板上输出3.3V电平。
:::