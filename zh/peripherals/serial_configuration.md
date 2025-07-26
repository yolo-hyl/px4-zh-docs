# 串口配置

PX4 为许多飞控端口定义了[默认功能](#默认串口配置)，这就是为什么你可以将 GPS 模块插入标记为 `GPS 1` 的端口，将遥控接收机插入 `RC IN`，或将遥测模块插入 `TELEM 1`，它们通常可以直接使用。

端口的功能可以通过适当的参数（在大多数情况下）完全配置。你可以将任何未使用的端口分配给任何功能，或重新分配端口以用于其他用途。

配置使得（例如）可以轻松实现以下操作：

- 在不同端口上运行 MAVLink，更改流式消息，或将 TELEM 端口切换为使用 ROS 2/XRCE-DDS。
- 更改端口的波特率或设置 UDP 端口。
- 设置双 GPS。
- 启用通过串口运行的传感器，例如某些[距离传感器](../sensor/rangefinders.md)。

::: info

- 某些端口无法配置，因为它们用于特定用途，例如系统控制台。
- 飞控上具体设备到端口名称的映射在 [Serial Port Mapping](../hardware/serial_port_mapping.md) 中有详细说明。
:::

## 配置参数

串口配置参数允许您将特定功能或对特定硬件的支持分配到特定端口。这些参数遵循`*_CONFIG`或`*_CFG`的命名模式。

::: info
_QGroundControl_ 仅显示固件中存在的服务/驱动程序的参数。
:::

当前包含的配置项如下：

- GNSS配置：
  - GPS模块：[GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG)，[GPS_2_CONFIG](../advanced_config/parameter_reference.md#GPS_2_CONFIG)（适用于大多数GPS/GNSS系统）
  - [Septentrio GNSS模块](../gps_compass/septentrio.md)：[SEP_PORT1_CFG](../advanced_config/parameter_reference.md#SEP_PORT1_CFG)，[SEP_PORT2_CFG](../advanced_config/parameter_reference.md#SEP_PORT2_CFG)
- [Iridium卫星无线电](../advanced_features/satcom_roadblock.md)：[ISBD_CONFIG](../advanced_config/parameter_reference.md#ISBD_CONFIG)
- [MAVLink端口](../peripherals/mavlink_peripherals.md)：[MAV_0_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG)，[MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG)，[MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG)
- VOXL电调：[VOXL_ESC_CONFIG](../advanced_config/parameter_reference.md#VOXL_ESC_CONFIG)
- MSP OSD：[MSP_OSD_CONFIG](../advanced_config/parameter_reference.md#MSP_OSD_CONFIG)
- 电调端口：[RC_PORT_CONFIG](../advanced_config/parameter_reference.md#RC_PORT_CONFIG)
- [FrSky遥测](../peripherals/frsky_telemetry.md)：[TEL_FRSKY_CONFIG](../advanced_config/parameter_reference.md#TEL_FRSKY_CONFIG)
- HoTT遥测：[TEL_HOTT_CONFIG](../advanced_config/parameter_reference.md#TEL_HOTT_CONFIG)
- [uXRCE-DDS](../middleware/uxrce_dds.md)端口：[UXRCE_DDS_CFG](../advanced_config/parameter_reference.md#UXRCE_DDS_CFG)
- 传感器（光流、距离传感器）：[SENS_CM8JL65_CFG](../advanced_config/parameter_reference.md#SENS_CM8JL65_CFG)，[SENS_LEDDAR1_CFG](../advanced_config/parameter_reference.md#SENS_LEDDAR1_CFG)，[SENS_SF0X_CFG](../advanced_config/parameter_reference.md#SENS_SF0X_CFG)，[SENS_TFLOW_CFG](../advanced_config/parameter_reference.md#SENS_TFLOW_CFG)，[SENS_TFMINI_CFG](../advanced_config/parameter_reference.md#SENS_TFMINI_CFG)，[SENS_ULAND_CFG](../advanced_config/parameter_reference.md#SENS_ULAND_CFG)，[SENS_VN_CFG](../advanced_config/parameter_reference.md#SENS_VN_CFG)
- CRSF遥控输入驱动：[RC_CRSF_PRT_CFG](../advanced_config/parameter_reference.md#RC_CRSF_PRT_CFG)
- Sagetech MXS：[MXS_SER_CFG](../advanced_config/parameter_reference.md#MXS_SER_CFG)
- 超宽带定位传感器：[UWB_PORT_CFG](../advanced_config/parameter_reference.md#UWB_PORT_CFG)
- DShot驱动：[DSHOT_TEL_CFG](../advanced_config/parameter_reference.md#DSHOT_TEL_CFG)

某些功能/特性可能会定义额外的配置参数，这些参数将遵循与端口配置前缀相似的命名模式。例如，`MAV_0_CONFIG`在特定端口启用MAVLink，但您可能还需要设置[MAV_0_FLOW_CTRL](../advanced_config/parameter_reference.md#MAV_0_FLOW_CTRL)，[MAV_0_FORWARD](../advanced_config/parameter_reference.md#MAV_0_FLOW_CTRL)，[MAV_0_MODE](../advanced_config/parameter_reference.md#MAV_0_MODE)等参数。

## 如何配置端口

所有串口驱动/端口的配置方式相同：

1. 将服务/外设的配置参数设置为将使用的端口。
1. 重启机体以使附加配置参数可见。
1. 将所选端口的波特率参数设置为所需值（例如 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD)）
1. 配置模块特定参数（即 MAVLink 流和数据速率配置）。

[GPS/Compass > 备用GPS](../gps_compass/index.md#dual_gps) 部分提供了在 _QGroundControl_ 中配置端口的实际示例（展示了如何使用 `GPS_2_CONFIG` 在 `TELEM 2` 端口上运行备用GPS）。

类似地，[PX4 以太网设置 > PX4 MAVLink 串口配置](../advanced_config/ethernet_setup.md#px4-mavlink-serial-port-configuration) 解释了以太网串口的设置，而 [MAVLink 外设 (OSD/GCS/伴飞计算机等)](../peripherals/mavlink_peripherals.md) 解释了 MAVLink 串口的配置。

## 端口冲突处理

端口冲突由系统启动时进行处理，确保特定端口上最多只运行一个服务。  
例如，不可能在特定串口设备上启动一个MAVLink实例，然后再启动使用同一串口设备的驱动程序。

:::warning  
目前尚无用户反馈有关端口冲突的问题。  
:::  

<a id="default_port_mapping"></a>

## 默认串口配置

:::tip
这些端口映射可以通过将相关配置参数设置为 _Disabled_ 来禁用。
:::

以下端口通常在所有开发板上映射到特定功能：

- `GPS 1` 配置为GPS端口（使用 [GPS_1_CONFIG](../advanced_config/parameter_reference.md#GPS_1_CONFIG)）。

  默认波特率通过 [gps driver](../modules/modules_driver.md#gps) 中的 [SER_GPS1_BAUD](../advanced_config/parameter_reference.md#SER_GPS1_BAUD) 参数设置，默认值为 _Auto_。
  该设置下GPS会自动检测波特率——但Trimble MB-Two需要显式设置为115200波特率。

- `RC IN` 配置为遥控输入（使用 [RC_PORT_CONFIG](../advanced_config/parameter_reference.md#RC_PORT_CONFIG)）。
- `TELEM 1` 配置为MAVLink串口，适用于通过 [telemetry module](../telemetry/index.md) 连接地面站（GCS）。

  配置使用 [MAV_0_CONFIG](../advanced_config/parameter_reference.md#MAV_0_CONFIG) 设置端口，[MAV_0_RATE](../advanced_config/parameter_reference.md#MAV_0_RATE) 设置波特率为57600，[MAV_0_MODE](../advanced_config/parameter_reference.md#MAV_1_MODE) 设置消息流模式为"Normal"。
  更多信息请参见：[MAVLink Peripherals (OSD/GCS/Companion Computers/etc.)](../peripherals/mavlink_peripherals.md)。

- `TELEM 2` 默认配置为MAVLink串口，适用于通过有线连接连接到机载/伴飞计算机。

  配置使用 [MAV_1_CONFIG](../advanced_config/parameter_reference.md#MAV_1_CONFIG) 设置端口，[MAV_1_RATE](../advanced_config/parameter_reference.md#MAV_1_RATE) 设置波特率，[MAV_1_MODE](../advanced_config/parameter_reference.md#MAV_2_MODE) 设置消息流模式为"Onboard"。
  更多信息请参见：[MAVLink Peripherals (OSD/GCS/Companion Computers/etc.)](../peripherals/mavlink_peripherals.md)。

- `Ethernet` 在配备以太网端口的Pixhawk设备上映射为MAVLink端口。

  配置使用 [MAV_2_CONFIG](../advanced_config/parameter_reference.md#MAV_2_CONFIG) 及适当的UDP端口等设置。
  更多信息请参见 [PX4 Ethernet Setup > PX4 MAVLink Serial Port Configuration](../advanced_config/ethernet_setup.md#px4-mavlink-serial-port-configuration) 和 [MAVLink Peripherals (OSD/GCS/Companion Computers/etc.)](../peripherals/mavlink_peripherals.md)。

- `USB-C`（通常用于连接QGroundControl的USB-C端口）

  默认配置为机载配置文件的MAVLink端口（用于伴飞计算机）。
  该端口的MAVLink配置是独立的（不使用 `MAV_X_CONFIG` 参数）。

  - [SYS_USB_AUTO](../advanced_config/parameter_reference.md#SYS_USB_AUTO) 设置端口是否设置为无特定协议、自动检测协议或设置通信链路为MAVLink。
  - [USB_MAV_MODE](../advanced_config/parameter_reference.md#USB_MAV_MODE) 设置MAVLink配置文件（当MAVLink被设置或检测到时使用）。

其他端口通常默认无分配功能（被禁用）。

## 故障排除

<a id="parameter_not_in_firmware"></a>

### _QGroundControl_ 缺失配置参数

_QGroundControl_ 仅显示固件中包含的服务/驱动程序参数。
如果某个参数缺失，则可能需要在固件中添加它。

::: info
PX4 固件在 [Pixhawk-series](../flight_controller/pixhawk_series.md) 板上默认包含大多数驱动程序。
受限于闪存容量的板可能注释掉/省略驱动程序（截至撰写时，仅影响基于 FMUv2 的板）。
:::

您可以通过在目标 [板](https://github.com/PX4/PX4-Autopilot/tree/main/boards/px4) 对应的 **default.px4board** 配置文件中启用驱动程序，将缺失的驱动程序包含在固件中。
例如，要启用 SRF02 驱动程序，您需要向 px4board 添加以下行：

```
CONFIG_DRIVERS_DISTANCE_SENSOR_SRF02=y
```

更简单的方法是使用 boardconfig，它会启动一个图形界面，您可以轻松地搜索、禁用和启用模块。
要启动 boardconfig，请输入：

```
make <vendor>_<board>_<label> boardconfig
```

然后您需要为您的平台构建固件，如 [Building PX4 Software](../dev_setup/building_px4.md) 中所述。

## 更多信息

- [MAVLink 外设（OSD/GCS/伴飞计算机等）](../peripherals/mavlink_peripherals.md)
- [PX4 以太网设置 > PX4 MAVLink 串口配置](../advanced_config/ethernet_setup.md#px4-mavlink-serial-port-configuration)
- [串口映射](../hardware/serial_port_mapping.md)