# Septentrio GNSS接收机

PX4支持基于mosaic-X5和mosaic-H接收器模块的Septentrio GNSS接收机。  
它通过[参数化](../advanced_config/parameter_reference.md#septentrio)的自动配置适配不同应用场景。  
输出由PX4中的Septentrio驱动程序处理，并提供给MAVLink和EKF等系统模块。

由于物理或技术特性，某些接收器特别推荐用于自动驾驶仪应用，包括：

- [AsteRx-m3 Pro](../gps_compass/septentrio_asterx-rib.md)  
  支持航向的双天线、超低功耗GNSS移动站接收器。

- [AsteRx-m3 Pro+](https://www.septentrio.com/en/products/gps/gnss-boards/asterx-m3-pro-plus)  
  支持航向的双天线、超低功耗多功能GNSS移动站和基准站接收器。

- [mosaic-go](../gps_compass/septentrio_mosaic-go.md)  
  基于mosaic-X5 GNSS接收器模块、支持L5频段的单天线评估套件。

- [mosaic-go heading](https://www.septentrio.com/en/products/gps/gnss-receiver-modules/mosaic-h-evaluation-kit)  
  基于mosaic-H GNSS接收器模块、支持航向的双天线评估套件。

## 支持的功能

- 通过MAVLink从地面控制站获取校正信息的RTK定位
- 基于mosaic-H接收器的双天线航向
- 使用两个单天线接收器的移动基座航向
- 接收器内部存储的SBF日志
- 飞控内部存储的接收器通信日志
- 自动波特率和端口检测
- 所有功能的自动参数化配置
- 来自MAVLink控制台的状态和健康监测## 快速入门

对于Septentrio GNSS接收器与飞控之间的物理连接，请参考针对[mosaic-go 接收器](septentrio_mosaic-go.md)和[AsteRx-m3 接收器与机器人接口板](../gps_compass/septentrio_asterx-rib.md)的硬件特定指南。

PX4要支持Septentrio接收器需要满足几个条件。首先，您加载在飞控上的固件需要包含Septentrio驱动程序。对于大多数PX4 v1.15版本的飞控板，这通常是默认支持的。

您可以通过打开_QGroundControl_，连接到运行目标固件的飞控，并检查参数配置界面中是否存在`Septentrio`参数组来确认这一点。

![Septentrio 驱动参数](../../assets/hardware/gps/septentrio_sbf/septentrio_driver_parameters.png)

::: tip
如果未看到`Septentrio`参数组，则需要在您的[PX4 板配置 (Kconfig)](../hardware/porting_guide_config.md)中设置`CONFIG_DRIVERS_GNSS_SEPTENTRIO=y`。
:::

接下来需要告知PX4接收器连接的串口。飞控上的串口名称通常由端口上方的标签标识。如果将接收器插入标记为`GPS 1`或`GPS & SAFETY`的端口，需要将[SEP_PORT1_CFG](../advanced_config/parameter_reference.md#SEP_PORT1_CFG)参数设置为`GPS 1`。同时需要确保没有其他驱动程序[配置使用该串口](../peripherals/serial_configuration.md)。

::: warning
默认情况下，`GPS`模块将配置为使用`GPS 1`端口。请确保将`GPS_1_CONFIG`设置为`Disabled`。
:::

随后PX4会自动配置连接的接收器，此时位置信息将在_QGroundControl_中显示，且GPS图标将显示常规状态信息。

注意您也可以使用第二个Septentrio GNSS模块，其配置方式类似。通常会将第二模块连接到标记为`GPS 2`的端口，该端口通过[SEP_PORT2_CFG](../advanced_config/parameter_reference.md#SEP_PORT2_CFG)参数进行配置。

## 串口配置

Septentrio GNSS接收器通过串口与飞控系统连接。该连接的配置包含两类参数。第一类是QGC中`Septentrio`参数组下的参数，用于选择接收器连接的物理端口。例如，若某接收器连接到标记为"GPS 2"的端口，需将`SEP_PORT1_CFG`设置为`GPS 2`（说明主接收器[因`SEP_PORT1_CFG`中的1]连接到次级GPS端口`GPS 2`）。

串口连接的波特率也具有可配置性。可设置为Septentrio GNSS接收器支持的任何波特率。驱动程序将始终检测接收器的波特率，若设置了特定波特率，将配置接收器使用该值；若未设置，则沿用接收器当前波特率。较高波特率可提供更快的连接速度，但在干扰较强或线缆质量较差时可能导致通信失败。使用非推荐波特率将无法正常工作，驱动程序将回退到默认值`230400`。当使用RTK定位或更高输出频率时，建议使用超过`460800`的波特率。

推荐的波特率如下：
- `57600`
- `115200`
- `230400`
- `460800`
- `921600`

::: info
驱动程序也支持`500000`、`576000`、`1000000`、`1500000`等非标准波特率，但应避免使用。
:::

## 自动配置

通常情况下，只要端口设置正确，驱动程序会自动配置连接的接收机。  
某些用户可能希望对配置进行额外修改，此时需要将 [SEP_AUTO_CONFIG](../advanced_config/parameter_reference.md#SEP_AUTO_CONFIG) 参数设为禁用自动配置。  
请注意，驱动程序始终会检测波特率和接收机端口以确保正常运行，无论是否禁用自动配置都会执行此操作。

许多 [其他参数](../advanced_config/parameter_reference.md#septentrio) 可用于更改接收机的配置方式。[SEP_CONST_USAGE](../advanced_config/parameter_reference.md#SEP_CONST_USAGE) 可用于选择PVT计算中包含/排除的星座。[SEP_OUTP_HZ](../advanced_config/parameter_reference.md#SEP_OUTP_HZ) 会更改PVT数据输出到飞控的频率。

[SEP_STREAM_MAIN](../advanced_config/parameter_reference.md#SEP_STREAM_MAIN) 和 [SEP_STREAM_LOG](../advanced_config/parameter_reference.md#SEP_STREAM_LOG) 会更改接收机主输出和日志输出使用的数据流。  
当您已将默认数据流用于其他用途时可以修改这些参数。  
请确保使用两个不同的数据流。将同一数据流同时用于两者会导致日志无法正确启动。

## 基于GNSS的航向

航向决定了机体的朝向。基于GNSS的航向可无需依赖内部指南针，避免电机或其他机体因素造成的干扰。

使用Septentrio GNSS接收器实现基于GNSS的航向有两种方式：
第一种是使用基于mosaic-H接收模块的接收器，例如mosaic-go heading、AsteRx-m3 Pro和AsteRx-m3 Pro+。连接这些设备并安装两个天线后，航向功能将自动启用。

第二种是使用两个独立接收器分别连接到两个端口，每个接收器配一个天线。此时需将参数[SEP_HARDW_SETUP](../advanced_config/parameter_reference.md#SEP_HARDW_SETUP)设置为`Moving base`，主接收器（由`SEP_PORT1_CFG`设置）将作为移动站。在移动站配置中切换主/基准站时，可交换`SEP_PORT1_CFG`和`SEP_PORT2_CFG`参数设置，或物理上更换连接的接收器。

天线至少需相隔30厘米以获得稳定的航向结果。在标准配置中，主天线应位于辅助天线后方。若采用其他配置方式，需相应调整[SEP_YAW_OFFS](../advanced_config/parameter_reference.md#SEP_YAW_OFFS)数值。若天线高度不一致，需调整[SEP_PITCH_OFFS](../advanced_config/parameter_reference.md#SEP_PITCH_OFFS)数值。

## 日志记录

Septentrio GNSS 接收器有两方式记录数据。
第一种是接收器内部记录特定的 SBF (Septentrio 二进制格式) 数据块。
第二种是记录飞行控制器与接收器之间的通信数据（接收的数据、发送的数据或所有通信），这些数据存储在飞行控制器的内部存储中。

### 接收器内部日志

驱动程序可以配置接收器将其数据记录到内部存储中，这在出现问题时可用于故障排查。
有三个参数可以更改日志配置：

- [SEP_LOG_HZ](../advanced_config/parameter_reference.md#SEP_LOG_HZ) 设置内部日志的频率，也可用于禁用日志记录。
- [SEP_LOG_FORCE](../advanced_config/parameter_reference.md#SEP_LOG_FORCE) 设置驱动程序是否覆盖日志流中现有消息（`Enabled`）或追加到消息后（`Disabled`）。
- [SEP_LOG_LEVEL](../advanced_config/parameter_reference.md#SEP_LOG_LEVEL) 设置内部日志的详细程度。
  有四个级别：

  | 级别    | 数据块                                                     |
  | ------- | ---------------------------------------------------------- |
  | Lite    | Comment+ReceiverStatus                                     |
  | Basic   | Comment+ReceiverStatus+PostProcess+Event                   |
  | Default | Comment+ReceiverStatus+PostProcess+Event+Support           |
  | Full    | Comment+ReceiverStatus+PostProcess+Event+Support+BBSamples |

### 飞行控制器日志

驱动程序还可以记录与接收器的所有通信。
可通过 `SEP_DUMP_COMM` 参数进行配置。
如需了解更多关于读取这些文件的信息，请参阅[日志指南](../dev_log/logging.md)。

## MAVLink控制台使用

[Septentrio驱动](../modules/modules_driver.md#septentrio)可以通过[MAVLink控制台](../debug/mavlink_shell.md#qgroundcontrol-mavlink-console)进行控制。
这允许执行驱动的启动和关闭、接收机重置、状态监控以及参数修改。
当遇到问题时，控制台可能会打印相关错误信息，帮助定位问题来源。
**当驱动是从控制台启动时，错误信息才会显示。**

```sh

```# 查看帮助

septentrio -h

```sh

```# 获取当前状态和统计信息
septentrio status# 启动驱动程序并连接接收机到端口 `/dev/ttyS0` 和# 自动配置为波特率 115200。同时使用接收器# 端口 `/dev/ttyS7` 并使用其当前波特率  
septentrio start -d /dev/ttyS0 -b 115200 -e /dev/ttyS7  

```

支持三种重置类型：

- `hot`: 重置接收器固件，但保留当前配置  
- `warm`: 重置接收器固件，保留当前配置但清除缓存的PVT数据  
- `cold`: 重置接收器固件并使用引导配置，同时清除卫星数据（如星历）  

```sh  
```# 对已连接的接收器执行热重置
septentrio reset hot