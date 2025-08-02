# 云台配置

本页说明如何配置和控制带有连接摄像头（或任何其他有效载荷）的云台。

## 概述

PX4 包含一个通用的云台/安装控制驱动程序，支持不同的输入和输出方法：

- 输入方法定义了用于控制由 PX4 管理的云台的协议。  
  这可能是遥控器、地面站（GCS）发送的 MAVLink 命令，或两者自动切换。  
- 输出方法定义了 PX4 如何与连接的云台通信。  
  推荐协议是 MAVLink v2，但你也可以直接连接到飞控的 PWM 输出端口。

PX4 接收输入信号并进行路由/转换，然后发送到输出端。  
任何输入方法都可以选择驱动任何输出。

输入和输出都通过参数进行配置。  
输入通过参数 [MNT_MODE_IN](../advanced_config/parameter_reference.md#MNT_MODE_IN) 设置。  
默认情况下，此参数设置为 `Disabled (-1)`，此时驱动程序不会运行。  
选择输入模式后，重启机体以启动云台驱动程序。

你应该将 `MNT_MODE_IN` 设置为以下之一：  
`RC (1)`、`MAVlink 云台协议 v2 (4)` 或 `Auto (0)`（其他选项已弃用）。  
如果选择 `Auto (0)`，云台将根据最新输入自动选择 RC 或 MAVLink 输入。  
注意：从 MAVLink 切换到 RC 需要大幅度的摇杆运动！

输出通过参数 [MNT_MODE_OUT](../advanced_config/parameter_reference.md#MNT_MODE_OUT) 设置。  
默认输出设置为 PXM 端口（`AUX (0)`）。  
如果你的云台支持 [MAVLink 云台协议 v2](https://mavlink.io/en/services/gimbal_v2.html)，则应选择 `MAVLink 云台协议 v2 (2)`。

设置云台驱动程序的完整参数列表可在 [参数参考 > 云台](../advanced_config/parameter_reference.md#mount) 中找到。  
以下描述了若干常见云台配置的相关设置。

## MAVLink 云台 (MNT_MODE_OUT=MAVLINK)

系统中每个物理云台设备必须拥有独立的高阶云台管理器，该管理器可通过地面站使用MAVLink云台协议进行发现。
地面站会向其需要控制的云台管理器发送高阶[MAVLink云台管理器](https://mavlink.io/en/services/gimbal_v2.html#gimbal-manager-messages)指令，管理器随后会发送适当的低阶"云台设备"指令来控制云台。

PX4可配置为云台管理器来控制单个云台设备（该设备可以是物理连接的，也可以是实现了[gimbal设备接口](https://mavlink.io/en/services/gimbal_v2.html#gimbal-device-messages)的MAVLink云台）。

要启用MAVLink云台，首先将参数[MNT_MODE_IN](../advanced_config/parameter_reference.md#MNT_MODE_IN)设置为`MAVLink云台协议v2`，并将[MNT_MODE_OUT](../advanced_config/parameter_reference.md#MNT_MODE_OUT)设置为`MAVLink云台协议v2`。

云台可通过任意空闲串口连接，具体方式请参考[MAVLink外设(GCS/OSD/伴侣设备)](../peripherals/mavlink_peripherals.md)中的说明（另见[串口配置](../peripherals/serial_configuration.md#serial-port-configuration)）。
例如，如果飞控上的`TELEM2`端口未被使用，可将其连接到云台并设置以下PX4参数：

- [MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG) 设置为 **TELEM2**（如果`MAV_1_CONFIG`已用于伴侣计算机，可改用`MAV_2_CONFIG`）。
- [MAV_1_MODE](../advanced_config/parameter_reference.md#MAV_1_MODE) 设置为 **云台**
- [MAV_1_FLOW_CTRL](../advanced_config/parameter_reference.md#MAV_1_FLOW_CTRL) 设置为 **关闭(0)**（极少云台会连接RST/CST线缆）。
- [MAV_1_FORWARD](../advanced_config/parameter_reference.md#MAV_1_FORWARD) 设置为 **启用**（注意：严格意义上并非必需，因为当`MAV_1_MODE`设置为云台时转发功能会自动启用）。
- [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD) 设置为制造商推荐的波特率。

### 多云台支持

PX4会为检测到的PWM云台或首个具有相同系统ID的MAVLink云台设备自动创建云台管理器（在任意接口上均可检测）。
它不会自动为检测到的其他MAVLink云台设备创建云台管理器。

要支持额外的MAVLink云台，需满足以下条件：

- 实现云台_manager_协议。
- 在MAVLink网络上对地面站和PX4可见。
  这可能需要在PX4、GCS和云台之间配置流量转发。
- 拥有唯一的组件ID，且该组件ID必须在7-255范围内。

## 云台在飞控PWM输出上 (MNT_MODE_OUT=AUX)

云台也可以通过连接到最多三个飞控PWM端口并设置输出模式为`MNT_MODE_OUT=AUX`进行控制。

用于控制云台的输出引脚在[执行器配置 > 输出](../config/actuators.md#actuator-outputs)中设置，通过选择任意三个未使用的执行器输出并分配以下输出功能：

- `云台横滚`：输出控制云台横滚。
- `云台俯仰`：输出控制云台俯仰。
- `云台偏航`：输出控制云台偏航。

例如，您可能有如下设置将云台横滚、俯仰和偏航分配到AUX1-3输出。

![云台执行器配置](../../assets/config/actuators/qgc_actuators_gimbal.png)

禁用、最大和最小值的PWM值可以通过与其他舵机相同的方式确定，使用[执行器测试滑块](../config/actuators.md#actuator-testing)确认每个滑块移动对应轴，并调整数值使云台在滑块禁用、低值和高值时处于适当位置。
云台文档中也可能提供这些数值。

## 任务中的云台控制

[云台管理器命令](https://mavlink.io/en/services/gimbal_v2.html#gimbal-manager-messages) 可在任务中使用（如果机体类型支持）。
例如 [MAV_CMD_DO_GIMBAL_MANAGER_PITCHYAW](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_GIMBAL_MANAGER_PITCHYAW) 支持在 [多旋翼任务模式](../flight_modes_mc/mission.md) 中使用。

理论上你可以通过指定 "Gimbal device id" 参数中的组件ID，向特定云台发送命令。
但截至撰写时（2024年12月）[暂不支持](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/gimbal/input_mavlink.cpp#L889)：所有任务命令都会发送到由 PX4 云台管理器管理的（唯一）云台（如果这是 MAVLink 云台，其组件ID由参数 [MNT_MAV_COMPID](../advanced_config/parameter_reference.md#MNT_MAV_COMPID) 定义，默认值为 [MAV_COMP_ID_GIMBAL (154)](https://mavlink.io/en/messages/common.html#MAV_COMP_ID_GIMBAL)）。

云台运动并非立即完成。
为确保在任务进入下一个项目前云台有足够时间到位（如果云台反馈未提供或丢失），应将 [MIS_COMMAND_TOUT](../advanced_config/parameter_reference.md#MIS_COMMAND_TOUT) 设置为大于云台全行程移动所需的时间。
超时后任务将自动进入下一个项目。

## 模拟 / SITL

以下模拟环境预配置了模拟云台。  
您可以通过 [QGroundControl 界面](#QGC测试) 或发送 [驱动命令](#驱动测试) 来测试云台。

::: tip
如果只需要测试 [云台驱动](../modules/modules_driver.md#gimbal)，则可以在任何模型或模拟器上进行测试。  
只需确保驱动程序运行，通过 MAVLink 控制台执行 `gimbal start`，然后配置驱动参数即可。
:::

### Gazebo

运行 [Gazebo](../sim_gazebo_gz/index.md) 模拟 [Gazebo 中的四旋翼（x500）云台（前向）](../sim_gazebo_gz/vehicles.md#x500-quadrotor-with-gimbal-front-facing)，请使用：

```sh
make px4_sitl gz_x500_gimbal
```

![Gazebo 中的四旋翼（x500）云台（前向）](../../assets/simulation/gazebo/vehicles/x500_gimbal.png)

### Gazebo Classic

运行 [Gazebo Classic](../sim_gazebo_classic/index.md) 模拟 [Typhoon H480 型号](../sim_gazebo_classic/vehicles.md#typhoon-h480-hexrotor)，请使用：

```sh
make px4_sitl gazebo-classic_typhoon_h480
```

![Gazebo Classic 中的 Typhoon H480](../../assets/simulation/gazebo_classic/vehicles/typhoon.jpg)

![Gazebo 云台模拟](../../assets/simulation/gazebo_classic/gimbal-simulation.png)

## 测试

### QGC测试

屏幕上的云台控制可用于移动/测试连接的MAVLink相机：

1. 启动您首选的 [模拟器](#simulation-sitl) 或连接到真实设备。
2. 打开QGroundControl并启用屏幕相机控制（应用设置）。

   ![Gazebo中的四旋翼(x500)与云台(前置)视图](../../assets/qgc/fly/gimbal_control_x500gz.png)

3. 确保机体已解锁并处于飞行状态，例如通过输入 `commander takeoff`。
4. 要更改云台目标位置，点击QGC GUI的上/下/左/右方向，或使用云台控制按钮（**Center**、**Tilt 90**、**Yaw lock**）。

### 驱动测试

可以通过在[QGroundControl MAVLink控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)发送`gimbal`驱动命令来测试云台：

1. 启动您首选的 [模拟器](#simulation-sitl) 或连接到真实设备。
2. 打开QGroundControl并连接到机体。
3. 通过菜单 **Analyze > Mavlink Console** 打开MAVLink控制台。
4. 确保机体已解锁并处于飞行状态，例如通过输入命令：`commander takeoff`。

要检查云台状态，请输入：

```sh
gimbal status
```

要将云台偏航设置为30度，请使用命令：

```sh
gimbal test yaw 30
```

更通用的设置角度或角速度命令格式为：

```sh
gimbal test <axis> <value>
```

- `axis`:
  - `<roll|pitch|yaw>` 表示角度
  - `<rollrate|pitchrate|yawrate>` 表示角速度
- `value`:
  - `<degrees>` 表示角度
  - `<degrees / second>` 表示角速度

要设置主控云台的MAVLink组件，请使用命令：

```sh
gimbal primary-control <sys_id> <comp_id>
```

- `sys_id`: MAVLink系统ID
- `comp_id`: MAVLink组件ID

其他命令请参阅 [`gimbal`](../modules/modules_driver.md#gimbal) 驱动模块文档。

### MAVLink测试

可以通过使用[MAVSDK](../robotics/mavsdk.md) 或其他MAVLink库发送MAVLink云台管理器命令来测试云台。

请注意，模拟的云台会自行稳定，因此如果发送MAVLink命令，请将 `stabilize` 标志设置为 `false`。