# ADS-B/FLARM/UTM 接收器：空中交通避让

PX4 支持通过使用 [ADS-B](https://en.wikipedia.org/wiki/Automatic_dependent_surveillance_%E2%80%93_broadcast)、[FLARM](https://en.wikipedia.org/wiki/FLARM) 或 [UTM](https://www.faa.gov/uas/research_development/traffic_management) 应答机的 [任务](../flying/missions.md) 实现基础的空中交通避让功能，这些设备使用标准的 MAVLink 接口。

当检测到潜在碰撞时，PX4 可以 _警告_、立即 [降落](../flight_modes_mc/land.md) 或 [返航](../flight_modes_mc/return.md)（具体行为取决于参数 [NAV_TRAFF_AVOID](#NAV_TRAFF_AVOID) 的值）。

## 支持的硬件

PX4避障功能支持通过MAVLink [ADSB_VEHICLE](https://mavlink.io/en/messages/common.html#ADSB_VEHICLE) 消息提供应答机数据的ADS-B或FLARM产品，以及通过MAVLink [UTM_GLOBAL_POSITION](https://mavlink.io/en/messages/common.html#UTM_GLOBAL_POSITION) 消息提供应答机数据的UTM产品。

已测试支持以下设备：

- [PingRX ADS-B接收器](https://uavionix.com/product/pingrx-pro/) (uAvionix)
- [FLARM](https://flarm.com/products/uav/atom-uav-flarm-for-drones/) <!-- I think originally https://flarm.com/products/powerflarm/uav/ -->## 硬件设置

任何设备均可连接至飞控器上任意未使用的串口。  
通常它们被连接到 `TELEM2` 端口（前提是该端口未被其他用途占用）。

### PingRX

PingRX 的 MAVLink 接口采用 JST ZHR-4 型对接连接器，引脚定义如下表所示：

| 引脚    | 信号    | 电压           |
| ------- | ------- | -------------- |
| 1 (红)  | RX (IN) | +5V容限       |
| 2 (黑)  | TX (OUT) |                |
| 3 (黑)  | 电源    | +4 至 6V       |
| 4 (黑)  | GND     | GND            |

PingRX 配备可直接连接到 [mRo Pixhawk](../flight_controller/mro_pixhawk.md) 上 TELEM2 端口（DF13-6P）的连接线缆。  
对于其他端口或开发板，需要自行准备线缆。

## FLARM

FLARM 配备了 DF-13 6 针接口，其针脚排列与 [mRo Pixhawk](../flight_controller/mro_pixhawk.md) 完全一致。

| 针脚     | 信号   | 电压        |
| ------- | -------- | ----------- |
| 1（红色） | VCC      | +4V 至 +36V |
| 2（黑色） | TX (OUT) | +3.3V       |
| 3（黑色） | RX (IN)  | +3.3V       |
| 4（黑色） | -        | +3.3V       |
| 5（黑色） | -        | +3.3V       |
| 6（黑色） | GND      | GND         |

::: info
飞控的 TX 和 RX 必须分别连接到 FLARM 的 RX 和 TX。
:::

## 软件配置

### 端口配置

接收机的配置方式与任何其他[MAVLink外设](../peripherals/mavlink_peripherals.md)相同。唯一需要特别设置的是端口波特率必须设置为57600，并使用低带宽配置文件（`MAV_X_MODE`）。

假设您已将设备连接到TELEM2端口，请按以下方式[设置参数](../advanced_config/parameters.md)：

- [MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG) = TELEM 2
- [MAV_1_MODE](../advanced_config/parameter_reference.md#MAV_1_MODE) = Normal
- [MAV_1_RATE](../advanced_config/parameter_reference.md#MAV_1_RATE) = 0（端口默认发送速率）
- [MAV_1_FORWARD](../advanced_config/parameter_reference.md#MAV_1_FORWARD) = Enabled

然后重启机体。

您现在会发现新增一个参数[SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD)，必须将其设置为57600。

### 配置交通避让

使用以下参数配置潜在碰撞时的响应动作：

| 参数                                                                                                   | 描述                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="NAV_TRAFF_AVOID"></a>[NAV_TRAFF_AVOID](../advanced_config/parameter_reference.md#NAV_TRAFF_AVOID)    | 启用交通避让模式并指定避让响应。0：禁用，1：仅警告，2：返航模式，3：降落模式。                                                                                             |
| <a id="NAV_TRAFF_A_HOR"></a>[NAV_TRAFF_A_HOR](../advanced_config/parameter_reference.md#NAV_TRAFF_A_HOR)    | 定义机体空域的水平半径（即地面平面上的空域）。                                                                                                                               |
| <a id="NAV_TRAFF_A_VER"></a>[NAV_TRAFF_A_VER](../advanced_config/parameter_reference.md#NAV_TRAFF_A_VER)    | 定义机体空域的垂直高度（上下方的圆柱体高度，另请参见[NAV_TRAFF_A_HOR](#NAV_TRAFF_A_HOR)）。                                                                                  |
| <a id="NAV_TRAFF_COLL_T"></a>[NAV_TRAFF_COLL_T](../advanced_config/parameter_reference.md#NAV_TRAFF_COLL_T) | 碰撞时间阈值。当预计碰撞时间低于此值时将触发避让（预计时间基于交通与机体的相对速度计算）。                                                                                   |## 实现

### ADSB/FLARM

PX4在任务执行期间监听有效的应答器报告。

当收到有效应答器报告时，PX4首先使用交通应答器信息估算交通航向和高度是否与机体空域产生交叉。
机体空域由半径[NAV_TRAFF_A_HOR](#NAV_TRAFF_A_HOR)和高度[NAV_TRAFF_A_VER](#NAV_TRAFF_A_VER)定义的圆柱形区域构成，机体位于该区域中心。
交通检测器随后根据相对速度检查与机体空域的交叉时间是否低于[NAV_TRAFF_COLL_T](#NAV_TRAFF_COLL_T)阈值。
若两项检查均为真，则启动[Traffic Avoidance Failsafe](../config/safety.md#traffic-avoidance-failsafe)动作，机体将发出警报、着陆或返航。

代码位于`Navigator::check_traffic` ([/src/modules/navigator/navigator_main.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/navigator/navigator_main.cpp))。

若MAVLink实例已配置，PX4还会将应答器数据转发至地面站（推荐配置）。
GUID的最后10位数字显示为无人机标识。

### UTM

PX4在任务执行期间监听`UTM_GLOBAL_POSITION` MAVLink消息。
当接收有效消息时，其有效性标志、位置和航向将映射到用于_ADS-B交通避让_的`transponder_report` UORB主题中。

其余实现方式与上述章节描述_完全一致_。

::: info
[UTM_GLOBAL_POSITION](https://mavlink.io/en/messages/common.html#UTM_GLOBAL_POSITION)包含ADSB应答器未提供的附加字段（参见[ADSB_VEHICLE](https://mavlink.io/en/messages/common.html#ADSB_VEHICLE)）。
当前实现会丢弃附加字段（包括机体计划航点的附加信息）。
:::

## 测试/模拟ADSB交通

你可以模拟ADSB交通用于测试。
请注意这要求你[构建PX4](../dev_setup/building_px4.md)。

::: info
模拟ADSB交通可以触发真实的自动返航动作。
在真实飞行中请谨慎使用！
:::

启用此功能的步骤：

1. 取消注释 `AdsbConflict::run_fake_traffic()` 中的代码([AdsbConflict.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/adsb/AdsbConflict.cpp#L342C1-L342C1))。
1. 重新构建并运行PX4。
1. 在 [QGroundControl MAVLink Shell](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 中执行 [`navigator fake_traffic` 命令](../modules/modules_controller.md#navigator) (或使用其他 [PX4控制台或MAVLink shell](../debug/consoles.md)，如PX4模拟器终端)。

`run_fake_traffic()` 中的代码随后会被执行。
你应该在控制台/MAVLink shell中看到ADSB警告，QGC也应显示ADSB交通弹窗。

默认情况下，`run_fake_traffic()` 会发布若干交通消息(它调用 [`AdsbConflict::fake_traffic()`](#fake-traffic-method) 发送每个报告)。
这些消息模拟ADSB交通中可能出现的冲突、无冲突情况，以及填满交通缓冲区。

::: details 关于测试方法的信息

相关方法定义在 [AdsbConflict.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/adsb/AdsbConflict.cpp#L342C1-L342C1) 中。

#### `run_fake_traffic()` 方法

当调用 `navigator fake_traffic` 命令时，`run_fake_traffic()` 方法会被执行。

该方法调用 `fake_traffic()` 方法生成围绕当前机体位置的模拟应答器消息。
它传入当前机体位置以及模拟交通信息，如呼号、距离、方向、高度差、速度和发射类型。

`run_fake_traffic()` 中的(注释)代码模拟了多种场景，包括冲突、无冲突，以及填满交通缓冲区。

#### `fake_traffic()` 方法

`AdsbConflict::fake_traffic()` 被 [`run_fake_traffic()`](#run-fake-traffic-method) 调用以创建单个ADSB应答器报告。

该方法接收多个参数，指定模拟交通的特征：

- `callsign`: 模拟应答器的呼号。
- `distance`: 当前机体到模拟机体的水平距离。
- `direction`: 从本机到模拟机体的NED方向(弧度)。
- `traffic_heading`: 交通的NED方向(弧度)。
- `altitude_diff`: 模拟交通的高度差。正值为上方。
- `hor_velocity`: 模拟交通的水平速度，单位m/s。
- `ver_velocity`: 模拟交通的垂直速度，单位m/s。
- `emitter_type`: 模拟机体的类型，枚举值。
- `icao_address`: ICAO地址。
- `lat_uav`: 本机纬度(用于定位模拟交通)
- `on_uav`: 本机经度(用于定位模拟交通)
- `alt_uav`: 机体高度(参考值 - 用于定位模拟交通)

该方法通过以下步骤创建模拟应答器消息：

- 根据UAV的位置、距离和方向计算交通的纬度和经度。
- 通过添加高度差到UAV的高度计算新高度。
- 用模拟交通数据填充 [TransponderReport](../msg_docs/TransponderReport.md) 话题。
- 如果板载支持通用唯一标识符(UUID)，该方法使用 `board_get_px4_guid` 获取UUID并复制到结构的 `uas_id` 字段。
  否则，生成模拟GUID。
- 使用 `orb_publish` 发布模拟交通消息。

:::

<!-- See also implementation PR: https://github.com/PX4/PX4-Autopilot/pull/21283 -->
<!-- See also bug to make this work without uncommenting: https://github.com/PX4/PX4-Autopilot/issues/21810 -->## 进一步信息

- [MAVLink 外设](../peripherals/mavlink_peripherals.md)
- [串口配置](../peripherals/serial_configuration.md)