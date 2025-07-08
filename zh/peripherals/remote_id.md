# 远程ID（Open Drone ID）

<Badge type="tip" text="PX4 v1.14" /><Badge type="warning" text="Experimental" />

:::warning Experimental
Remote ID支持是实验性功能。
:::

Remote ID是日本、美国和欧盟政府强制要求的无人机技术，旨在实现无人机与其他航空器的安全空域共享。该规范要求无人机广播数据，如：实时位置/高度、序列号、操作员ID/位置、状态等。

PX4支持符合FAA [标准Remote ID规则](https://www.faa.gov/uas/getting_started/remote_id) 的Remote ID模块。这些模块设计为集成到机体中，并使用自动驾驶仪提供的id、位置和其他信息，广播Open Drone ID消息（Open Drone ID是Remote ID的开源实现）。"标准规则"模块相较于"广播规则"模块具有更少的限制，后者是独立模块并集成GPS，与自动驾驶仪没有通信。

## 支持的硬件

::: warning
Remote ID 硬件只能通过 DroneCAN 连接到 `main` 分支构建版本（PX4 v1.15 之后的构建）。
:::

PX4 支持以下 Remote ID 硬件：

- [Open Drone ID](https://mavlink.io/en/services/opendroneid.html) MAVLink 协议<Badge type="tip" text="PX4 v1.14" />
- 通过 CAN 的 Remote ID<Badge type="tip" text="PX4 main (v1.16)" />

已测试以下设备：

- [Cube ID](https://docs.cubepilot.org/user-guides/cube-id/cube-id) (CubePilot)
- [Db201](https://dronescout.co/dronebeacon-mavlink-remote-id-transponder/) (BlueMark) - 通过串口测试。未通过 CAN 端口测试。
- [Db202mav](https://dronescout.co/dronebeacon-mavlink-remote-id-transponder/) (BlueMark) - 无 CAN 端口的低成本版本。
- [Holybro RemoteID Module](https://holybro.com/products/remote-id) (Holybro)

其他支持 Open Drone ID 协议和 DroneCAN 的设备也应兼容（但尚未经过测试）。

## 硬件设置

Remote ID设备可连接到飞控上的任意空闲串口或CAN接口。最常见的是直接连接到`TELEM2`端口（如果该端口未被其他用途占用），因为该端口默认已配置为MAVLink模式。

### Cube ID

[Cube ID](https://docs.cubepilot.org/user-guides/cube-id/cube-id)可通过串口或CAN接口连接。该设备配有6针和4针JST-GH 1.25mm线缆，可直接连接到大多数最新Pixhawk飞控的`TELEM`串口和`CAN`端口。

#### Cube ID串口

若使用其他端口或连接器不同的飞控，可能需要修改线缆。串口引脚定义如下：
- 飞控的TX和RX必须分别连接到Remote ID的RX和TX

![Cube ID串口](../../assets/hardware/remote_id/cube_id/serial_port_connector.jpg)

| 引脚    | 信号     | 电压 |
| ------- | -------- | ---- |
| 1 (red) | VCC_5V   | 5V   |
| 2 (blk) | TX (OUT) |      |
| 3 (blk) | RX (IN)  |      |
| 4 (blk) | GND      | 0    |

#### Cube ID CAN端口

![Cube ID CAN端口](../../assets/hardware/remote_id/cube_id/can_connector.png)

| 引脚    | 信号   | 电压 |
| ------- | ------ | ---- |
| 1 (red) | VCC_5V | 5V   |
| 2 (red) | CAN_H  |      |
| 3 (blk) | CAN_L  |      |
| 4 (blk) | GND    | 0    |

#### Cube ID固件

Cube ID使用专有固件（不同于[ArduRemoteID](https://github.com/ArduPilot/ArduRemoteID)的其他Remote ID信标）。

固件升级说明请参见[Cube ID > 更新](https://docs.cubepilot.org/user-guides/cube-id/cube-id#updating)。

### BlueMark Db201/Db202mav

[Db201](https://dronescout.co/dronebeacon-mavlink-remote-id-transponder/) 或 [Db202mav](https://dronescout.co/dronebeacon-mavlink-remote-id-transponder/) 可通过其串口连接。

::: info
`Db201`也配有CAN端口，应在PX4 `main`版本中正常工作
然而该功能尚未经过验证
:::

该设备配有6针JST-GH 1.25mm线缆，可直接连接到大多数Pixhawk飞控的`TELEM`端口。

若使用其他串口（如引脚数较少的接口）或连接器不同的飞控，可能需要修改线缆。端口引脚定义可参考[用户手册](https://download.bluemark.io/db200.pdf)。

信标出厂时已预装最新[ArduRemoteID](https://github.com/ArduPilot/ArduRemoteID)固件。如需通过网页界面更新固件，[用户手册](https://download.bluemark.io/db200.pdf)提供了详细说明。

信标安装等通用设置也可参考[用户手册](https://download.bluemark.io/db200.pdf)。

### Holybro Remote ID模块

[Holybro Remote ID模块](https://holybro.com/products/remote-id) 可通过串口或CAN接口连接。该模块配有6针JST-GH 1.25mm线缆，可直接连接到Pixhawk 6C/6X或Cube Orange等最新Pixhawk飞控的`TELEM`端口。

模块出厂时已预装最新[ArduRemoteID](https://github.com/ArduPilot/ArduRemoteID)固件。[用户手册](https://docs.holybro.com/radio/remote-id)提供了通过网页界面配置和更新固件的说明（如需）。

#### Holybro引脚定义

![Holybro Remote ID引脚定义](../../assets/peripherals/remoteid_holybro/holybro_remote_id_pinout.jpg)

## PX4 配置

### MAVLink 端口配置

通过串口连接的远程ID硬件配置方式与任何其他[MAVLink 外设](../peripherals/mavlink_peripherals.md)相同。

假设您已将设备连接到 `TELEM2` 端口，[设置以下参数](../advanced_config/parameters.md)：

- [MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG) = `TELEM 2`
- [MAV_1_MODE](../advanced_config/parameter_reference.md#MAV_1_MODE) = Normal
- [MAV_1_RATE](../advanced_config/parameter_reference.md#MAV_1_RATE) = 0（默认发送速率）
- [MAV_1_FORWARD](../advanced_config/parameter_reference.md#MAV_1_FORWARD) = Enabled

然后重启机体。

此时会出现一个新的参数 [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD)。
所需波特率取决于使用的远程ID设备（对于 Cube ID 必须设置为 57600）。

<!-- 理论上，支持WiFi或有线以太网的远程ID（或其他MAVLink外设）也可以通过这些网络连接。 -->

### DroneCAN 配置

通过CAN总线连接的远程ID硬件配置方式与任何其他[DroneCAN 硬件](../dronecan/index.md#px4-configuration)相同。

具体来说，您需要通过设置 [`UAVCAN_ENABLE`](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 为非零值来[启用DroneCAN](../dronecan/index.md#enabling-dronecan)。

::: tip
[CAN远程ID无法工作](../peripherals/remote_id.md#can-remote-id-not-working) 说明了如何测试此配置，以及在需要时如何调整远程ID设置。
:::

### 启用远程ID

无需显式启用远程ID（当前实现中支持的远程ID消息默认会流式传输或需要请求，即使未连接远程ID设备）。

### 基于远程ID的禁止起飞

若仅允许在远程ID就绪时才允许起飞，[设置](../advanced_config/parameters.md#conditional-parameters)参数 [COM_ARM_ODID](#COM_ARM_ODID) 为 `2`（默认禁用）。

| 参数                                                                                       | 描述                                                                                                                                                                            |
| ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="COM_ARM_ODID"></a>[COM_ARM_ODID](../advanced_config/parameter_reference.md#COM_ARM_ODID) | 启用无人机ID系统检测和健康检查。`0`: 禁用（默认），`1`: 未检测到远程ID时警告但仍允许起飞，`2`: 仅在存在远程ID时允许起飞。 |## 模块广播测试

集成人员应测试远程ID模块是否广播了正确的信息，例如无人机位置、ID、操作员ID等。  
这可以通过在移动设备上使用第三方应用程序最轻松地完成：

- [Drone Scanner](https://github.com/dronetag/drone-scanner) (Google Play 或 Apple App store)  
- [OpenDroneID OSM](https://play.google.com/store/apps/details?id=org.opendroneid.android_osm&hl=en&gl=US) (Google Play)

## 实现  

PX4 v1.14 默认在以下流模式中发送这些消息（流模式：normal、onboard、usb、onboard low bandwidth）：  

- [OPEN_DRONE_ID_LOCATION](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_LOCATION) (1 Hz) - 机体位置、海拔、方向和速度。  
- [OPEN_DRONE_ID_SYSTEM](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_SYSTEM) (1 Hz) 操作员位置/海拔、多架飞机信息（群组/蜂群，如适用）、完整时间戳和可能的类别/分类信息。  

  - 实现假设操作员位于机体的起始位置（目前不支持从地面控制站获取操作员位置）。  
    这被认为适用于仅广播的 Remote IDs。  

以下消息可通过请求发送（使用 [MAV_CMD_SET_MESSAGE_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_MESSAGE_INTERVAL)）：  

- [OPEN_DRONE_ID_BASIC_ID](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_BASIC_ID) - 机体身份信息（本质上是一个序列号）  
  - PX4 v1.14 指定了序列号 ([MAV_ODID_ID_TYPE_SERIAL_NUMBER](https://mavlink.io/en/messages/common.html#MAV_ODID_ID_TYPE_SERIAL_NUMBER))，但未使用所需的格式（ANSI/CTA-2063 格式）。  

如果参数 [COM_ARM_ODID](../advanced_config/parameter_reference.md#COM_ARM_ODID) 设置为 `2`，PX4 会根据 Remote ID 健康状态禁止上锁。  
机体随后需要 Remote ID 的 `HEARTBEAT` 消息作为上锁的前提条件。  
您也可以将参数设置为 `1` 以仅发出警告，但仍允许在未检测到 Remote ID `HEARTBEAT` 消息时上锁。  

以下 Open Drone ID MAVLink 消息在 PX4 v1.14 中不支持（将在 [PX4#21647](https://github.com/PX4/PX4-Autopilot/pull/21647) 中添加）：  

- [OPEN_DRONE_ID_AUTHENTICATION](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_AUTHENTICATION) - 提供机体的认证数据。  
- [OPEN_DRONE_ID_SELF_ID](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_SELF_ID) - 操作员身份（纯文本）。  
- [OPEN_DRONE_ID_OPERATOR_ID](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_OPERATOR_ID) - 操作员身份。  
- [OPEN_DRONE_ID_ARM_STATUS](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_ARM_STATUS) - Remote ID 硬件状态。  
  用作机体上锁的条件，并用于 Remote ID 健康检查。  
- [OPEN_DRONE_ID_SYSTEM_UPDATE](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_SYSTEM_UPDATE) - `OPEN_DRONE_ID_SYSTEM` 的子集，可发送更高频率的信息。

## 合规性

PX4 在 1.14 版本中可能不符合相关规范（这也是该功能目前仍处于实验性阶段的原因）。
已成立工作组评估现有差距。

已知问题包括：

- 机体必须在接收到 Remote ID [OPEN_DRONE_ID_ARM_STATUS](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_ARM_STATUS) 消息（状态需表明远程ID硬件已准备好广播）时才能上锁。
  - PX4 v1.14 不处理 `OPEN_DRONE_ID_ARM_STATUS`，上锁仅依赖于 Remote ID 设备的 `HEARTBEAT`。
- 远程ID的健康状态同时依赖于接收 `HEARTBEAT` 和 `OPEN_DRONE_ID_ARM_STATUS`。
  飞行过程中，若远程ID未上锁状态必须通过 [OPEN_DRONE_ID_LOCATION.status](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_LOCATION) 作为远程ID故障进行广播。
  - PX4 v1.14 尚未接收 `OPEN_DRONE_ID_ARM_STATUS`。
- 若存在 `OPEN_DRONE_ID_ARM_STATUS` 必须转发到 GCS 以进行额外的错误报告。
- [OPEN_DRONE_ID_BASIC_ID](https://mavlink.io/en/messages/common.html#OPEN_DRONE_ID_BASIC_ID) 中的序列号格式无效（非 ANSI/CTA-2063 格式）。
- 预期的机体ID应具备防篡改特性。

[PX4-Autopilot/21647](https://github.com/PX4/PX4-Autopilot/pull/21647) 计划解决上述已知问题。

## 故障排除

### CAN 远程ID无法工作

::: info
此信息基于CubePilot的CAN Cube ID测试。
_应该_同样适用于其他厂商的CAN远程ID模块。
:::

确认远程ID是否正常工作的方法：

- 检查QGroundControl [MAVLink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) (QGC **Analyze Tools > MAVLink Inspector**)中是否出现`OPEN_DRONE_ID_BASIC_ID`和`OPEN_DRONE_ID_LOCATION`消息。

- 如果没有这些消息，检查UAVCAN列表中是否出现Remote_ID节点。

  在[QGroundControl MAVLink Console](../debug/mavlink_shell.md#qgroundcontrol-mavlink-console)中运行以下命令：

  ```sh
  uavcan status
  ```

  连接的CAN节点应出现在列表中。
  如果系统中只有一个CAN组件（远程ID），列表可能如下所示：

  ```plain
  Online nodes (Node ID, Health, Mode):
     125 OK         OPERAT
  ```

  节点没有"名称"，如果有多个CAN节点，可以通过对比显示节点数与系统预期节点数是否匹配来判断。
  或者可以分别连接和断开远程ID后运行`uavcan status`并对比结果（这样可以确定远程ID模块的节点ID）。

如果远程ID CAN节点存在但消息未被接收，则可能需要配置远程ID本身：

1. 打开QGroundControl
2. 进入[应用设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/general.html): **Application Settings > General > Miscellaneous**。
3. 选择`Enable Remote ID`。
   此时应出现Remote ID标签页。

   ::: info
   如果该选项不存在，可能使用的是QGC的最新版本。
   此时可直接进入: **Application Settings > Remote ID**。
   :::

4. 输入基础信息、操作者信息和自定义ID信息。

配置完成后，再次检查MAVLink Inspector，并确认`OPEN_DRONE_ID_BASIC_ID`和`OPEN_DRONE_ID_LOCATION`消息是否已出现。

## 另请参见

- [无人机远程识别](https://www.faa.gov/uas/getting_started/remote_id) (FAA)