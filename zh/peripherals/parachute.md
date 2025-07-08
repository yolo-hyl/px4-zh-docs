# 降落伞

PX4 可配置在 [飞行终止](../advanced_config/flight_termination.md) 时触发降落伞。

降落伞可通过自由PWM输出或MAVLink连接。

::: info
飞行终止时 PX4 会关闭所有控制器并设置所有PWM输出为默认安全值（包括连接到PWM输出的设备），并触发任何连接的MAVLink降落伞。

因此您可以使用此功能激活连接到不同输出的多个互补安全设备。
详情请参见 [飞行终止配置](../advanced_config/flight_termination.md)。
:::

## 使用降落伞

使用降落伞时需注意以下事项：

- 降落伞不能保证机体不会被毁坏或造成伤害！
  您必须始终牢记飞行安全。
- 降落伞需要谨慎使用才能有效。
  例如，必须正确折叠。
- 降落伞有最低有效高度。
- 当机体倒飞时降落伞可能触发。
  这会增加减速所需时间，可能导致无人机压坏降落伞。
- 降落伞仅在飞控供电且PX4正常运行时才会展开（除非独立触发）。
  如果飞控堆栈崩溃则不会展开。

## 降落伞设置

飞行终止（因此降落伞展开）可能由安全检查（如遥控丢失、地理围栏违规等）、姿态触发器和其他故障检测检查触发，或由地面站命令触发。
飞行终止时 PX4 会将PWM输出设置为"默认安全值"（默认安全值关闭电机，但可用于打开/触发降落伞）。
如果连接了MAVLink降落伞且状态正常，则会发送命令激活它。

降落伞设置包括：

- 配置 [飞行终止](../advanced_config/flight_termination.md) 为需要展开降落伞的安全和故障情况的适当操作。
- 配置PX4在飞行终止时展开降落伞（适当设置PWM输出级别或发送MAVLink降落伞展开命令）。
- 配置PX4输出级别在默认安全时禁用电机。
  这是默认设置通常无需配置（舵机的默认中心值）。

### 启用飞行终止

启用飞行终止：

- 设置 [安全](../config/safety.md) 检查中需要触发降落伞的情况为 _飞行终止_。
- 设置 [故障检测器](../config/safety.md#failure-detector) 的俯仰角、滚转角和时间触发器用于坠机/翻转检测，并禁用故障/IMU超时断路器（即设置 [CBRK_FLIGHTTERM=0](../advanced_config/parameter_reference.md#CBRK_FLIGHTTERM)）。

::: info
您也可以配置 [外部自动触发系统(ATS)](../config/safety.md#external-automatic-trigger-system-ats) 进行故障检测。
:::

### 降落伞输出总线设置

如果降落伞通过PWM或CAN输出触发，则必须首先连接到未使用的输出。
您可能还需要单独为降落伞舵机供电。
这可以通过将5V BEC连接到飞控舵机总线并从中为降落伞供电实现。

然后需要确保在默认安全时降落伞引脚设置为触发降落伞的值：

- 在QGroundControl中打开 [执行器](../config/actuators.md)
- 将 _降落伞_ 功能分配到任何未使用的输出（以下设置为 `AUX6` 输出）：

  ![执行器 - 降落伞 (QGC)](../../assets/config/actuators/qgc_actuators_parachute.png)

- 为降落伞设置适当的PWM值。
  默认安全触发时输出会自动设置为最大PWM值。

  ::: info
  对于 [Fruity Chutes](https://fruitychutes.com/buyachute/drone-and-uav-parachute-recovery-c-21/harrier-drone-parachute-launcher-c-21_33/) 的弹簧加载发射器，最小PWM值应在700-1000ms之间，最大值在1800-2200ms之间。
  :::

### MAVLink 降落伞设置

PX4 通过发送 [MAV_CMD_DO_PARACHUTE](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_PARACHUTE) 命令与 [PARACHUTE_RELEASE](https://mavlink.io/en/messages/common.html#PARACHUTE_ACTION) 动作触发连接且状态正常的降落伞。

通过设置参数 [COM_PARACHUTE=1](../advanced_config/parameter_reference.md#COM_PARACHUTE) 启用MAVLink降落伞支持。
PX4 将通过 [SYS_STATUS](https://mavlink.io/en/messages/common.html#SYS_STATUS) 消息中的 [MAV_SYS_STATUS_RECOVERY_SYSTEM](https://mavlink.io/en/messages/common.html#MAV_SYS_STATUS_RECOVERY_SYSTEM) 位来指示降落伞状态：

- `SYS_STATUS.onboard_control_sensors_present_extended`: 存在MAVLink降落伞（基于心跳检测）。
- `SYS_STATUS.onboard_control_sensors_enabled_extended`: ?
- `SYS_STATUS.onboard_control_sensors_health_extended`: MAVLink降落伞状态正常（基于心跳检测）。

MAVLink降落伞需要发送 [HEARTBEAT](https://mavlink.io/en/messages/common.html#HEARTBEAT) 消息，且 `HEARTBEAT.type` 必须为 [MAV_TYPE_PARACHUTE](https://mavlink.io/en/messages/common.html#MAV_TYPE_PARACHUTE)。

<!-- PX4 v1.13支持添加于此: https://github.com/PX4/PX4-Autopilot/pull/18589 -->

## 降落伞测试

:::warning
首次测试时，请在无螺旋桨和未加载降落伞装置的情况下进行！
:::

::: info
无法从终止状态恢复！
后续测试需要重启/重新供电机体。
:::

降落伞将在 [飞行终止](../advanced_config/flight_termination.md) 时触发。

测试真实降落伞最简单的方法是启用 [故障检测器姿态触发](../config/safety.md#attitude-trigger) 并倾斜机体。

您也可以模拟降落伞/飞行终止：[Gazebo Classic > 模拟降落伞/飞行终止](../sim_gazebo_classic/index.md#simulated-parachute-flight-termination)。