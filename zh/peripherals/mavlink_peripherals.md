# MAVLink 外设 (地面站/屏幕显示/云台/摄像头/伴随计算机)

地面控制站 (GCS)、屏幕显示 (OSD)、MAVLink 摄像头和云台、远程ID、伴随计算机、ADS-B接收器以及其他MAVLink外设通过不同的串口与PX4交互，使用独立的MAVLink数据流进行通信。

为了配置特定串口用于与特定外设的MAVLink通信，我们使用 [串口配置](../peripherals/serial_configuration.md)，将其中一个抽象的"MAVLink实例"配置参数分配给目标串口。  
随后我们通过选定MAVLink实例关联的参数设置该MAVLink通道的其他属性，使其符合特定外设的要求。

下文描述了最相关的参数（完整参数列表请参见[参数参考 > MAVLink](../advanced_config/parameter_reference.md#mavlink)）。

## MAVLink 实例

为了将特定外设分配到串口，我们使用了 _MAVLink 实例_ 的概念。

每个 MAVLink 实例代表一个可以应用于特定端口的 MAVLink 配置。目前定义了三个 MAVLink _实例_，每个实例由参数 [MAV_X_CONFIG](#MAV_X_CONFIG) 表示，其中 X 为 0、1、2。

每个实例都有相关参数，可用于定义该端口上实例的属性，例如流消息集（参见下方 [MAV_X_MODE](#MAV_X_MODE)）、数据速率（[MAV_X_RATE](#MAV_X_RATE)）、是否将入站流量转发到其他 MAVLink 实例（[MAV_X_FORWARD](#MAV_X_FORWARD)）等。

::: info
MAVLink 实例是特定 MAVLink 配置的抽象概念。名称中的数字无特定含义；您可以将任何实例分配到任何端口。
:::

每个实例的参数如下：

- <a id="MAV_X_CONFIG"></a>[MAV_X_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG) - 为该实例 "X" 设置串口（UART），其中 X 为 0、1、2。  
  可以是任何未使用的端口，例如：`TELEM2`、`TELEM3`、`GPS2` 等。  
  更多信息请参见 [串口配置](../peripherals/serial_configuration.md)。
- <a id="MAV_X_MODE"></a>[MAV_X_MODE](../advanced_config/parameter_reference.md#MAV_0_MODE) - 指定遥测模式/目标（当前实例的流消息集及其速率）。  
  默认值包括：

  - _Normal_：适用于地面站的标准消息集。  
  - _Custom_ 或 _Magic_：无（在默认 PX4 实现中）。  
    开发新模式时可使用此模式进行测试。  
  - _Onboard_：适用于伴飞计算机的标准消息集。  
  - _OSD_：适用于OSD系统的标准消息集。  
  - _Config_：适用于高速链路（如USB）的标准消息集和速率配置。  
  - _Minimal_：适用于高延迟链路连接的地面站的最小消息集。  
  - _External Vision_：适用于外部视觉系统的消息。  
  - _Gimbal_：适用于云台的消息。注意，这也会启用 [消息转发](#MAV_X_FORWARD)。  
  - _Onboard Low Bandwidth_：适用于低速链路连接的伴飞计算机的标准消息集。  
  - _uAvionix_：适用于uAvionix ADS-B信标的消。

  ::: info
  如果需要查找每种模式的具体消息集，请在 [/src/modules/mavlink/mavlink_main.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_main.cpp) 中搜索 `MAVLINK_MODE_`。
  :::

  :::tip
  模式仅定义 _默认_ 消息和速率。  
  连接的 MAVLink 系统仍可通过 [MAV_CMD_SET_MESSAGE_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_MESSAGE_INTERVAL) 请求其所需的流/速率。
  :::

- <a id="MAV_X_RATE"></a>[MAV_X_RATE](../advanced_config/parameter_reference.md#MAV_0_MODE) - 设置此实例的 _最大数据速率_（字节/秒）。  
  - 这是所有单个消息流的综合速率（如果总速率超过此值，单个消息的速率会降低）。  
  - 默认设置通常可以接受，但如果遥测链路饱和且丢弃太多消息，可能需要降低速率。  
  - 值为 0 时，数据速率为理论值的一半。
- <a id="MAV_X_FORWARD"></a>[MAV_X_FORWARD](../advanced_config/parameter_reference.md#MAV_0_FORWARD) - 启用将当前实例接收到的 MAVLink 数据包转发到其他接口。  
  例如，可以用于在地面站和伴飞计算机之间传递消息，使地面站能够与连接到伴飞计算机的 MAVLink 相机通信。

接下来，需要为上述分配的串口（在 `MAV_X_CONFIG` 中）设置波特率。

:::tip
您需要重启 PX4 以使参数在 QGroundControl 中生效。
:::

使用的参数取决于 [分配的串口](../advanced_config/parameter_reference.md#serial) - 例如：`SER_GPS1_BAUD`、`SER_TEL2_BAUD` 等。  
使用值取决于连接类型和所连接 MAVLink 外设的性能。

## 默认 MAVLink 端口 {#default_ports}

### TELEM1

`TELEM 1` 端口通常默认配置为地面控制站（GCS）遥测流（"Normal" 模式）。

为此，MAVLink 实例 0 的默认串口映射如下：

- [MAV_0_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG) = `TELEM 1`
- [MAV_0_MODE](../advanced_config/parameter_reference.md#MAV_0_MODE) = `Normal`
- [MAV_0_RATE](../advanced_config/parameter_reference.md#MAV_0_RATE)= `1200` 字节/秒
- [MAV_0_FORWARD](../advanced_config/parameter_reference.md#MAV_0_FORWARD) = `True`
- [SER_TEL1_BAUD](../advanced_config/parameter_reference.md#SER_TEL1_BAUD) = `57600`

### TELEM2

`TELEM 2` 端口通常默认配置为协处理器遥测流（"Onboard" 模式）。

为此，MAVLink 实例 0 的默认串口映射如下：

- [MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG) = `TELEM 2`
- [MAV_1_MODE](../advanced_config/parameter_reference.md#MAV_0_MODE) = `Onboard`
- [MAV_1_RATE](../advanced_config/parameter_reference.md#MAV_0_RATE)= `0`（半速）
- [MAV_1_FORWARD](../advanced_config/parameter_reference.md#MAV_0_FORWARD) = `Disabled`
- [SER_TEL2_BAUD](../advanced_config/parameter_reference.md#SER_TEL2_BAUD) = `921600`

### ETHERNET

配备以太网端口的 Pixhawk 5x 设备（及后续型号）默认配置以太网端口用于连接 GCS：

在此硬件上，MAVLink 实例 2 的默认串口映射如下：

- [MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG) = `Ethernet` (1000)
- [MAV_2_BROADCAST](../advanced_config/parameter_reference.md#MAV_2_BROADCAST) = `1`
- [MAV_2_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) = `0`（normal/GCS）
- [MAV_2_RADIO_CTL](../advanced_config/parameter_reference.md#MAV_2_RADIO_CTL) = `0`
- [MAV_2_RATE](../advanced_config/parameter_reference.md#MAV_2_RATE) = `100000`
- [MAV_2_REMOTE_PRT](../advanced_config/parameter_reference.md#MAV_2_REMOTE_PRT)= `14550` (GCS)
- [MAV_2_UDP_PRT](../advanced_config/parameter_reference.md#MAV_2_UDP_PRT) = `14550` (GCS)

如需更多信息，请参阅：[PX4 以太网配置](../advanced_config/ethernet_setup.md)

## 设备特定设置

特定 MAVLink 组件的设置说明链接：

- [MAVLink相机(Camera Protocol v2) > PX4配置](../camera/mavlink_v2_camera.md#px4-configuration)
- [云台配置 > MAVLink云台(MNT_MODE_OUT=MAVLINK)](../advanced/gimbal_control.md#mavlink-gimbal-mnt-mode-out-mavlink)

## 另请参见

- [串口配置](../peripherals/serial_configuration.md)
- [PX4以太网配置 > PX4 MAVLink串口配置](../advanced_config/ethernet_setup.md#px4-mavlink-serial-port-configuration)
- [串口映射](../hardware/serial_port_mapping.md)