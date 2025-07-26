# SiK 无线电

[SiK radio](https://github.com/LorenzMeier/SiK) 是用于遥测无线电的固件和工具集合。

PX4 与使用 _SiK_ 协议的无线电设备兼容。
SiK 无线电通常配有合适的连接器/电缆，可直接连接到 [Pixhawk 系列](../flight_controller/pixhawk_series.md) 飞控器
（某些情况下可能需要获取合适的电缆/连接器）。
通常需要成对设备 - 一个用于机体，一个用于地面站。

SiK 无线电硬件可从不同制造商/商店获取，支持不同范围和外形尺寸的变种。

![SiK 无线电](../../assets/hardware/telemetry/holybro_sik_radio.jpg)

## 供应商

- [Holybro 遥测无线电](../telemetry/holybro_sik_radio.md)
- [RFD900 遥测无线电](../telemetry/rfd900_telemetry.md)
- [ThunderFly TFSIK01 遥测无线电](../telemetry/tfsik_telemetry.md)
- <del>_HKPilot 遥测无线电_</del>（已停产）
- <del>_3DR 遥测无线电_</del>（已停产）

## 设置/配置

地面站连接的无线电通过 USB 连接（基本即插即用）。

机体连接的无线电连接到飞控器的 `TELEM1` 接口，通常无需进一步配置。

## 固件更新

从大多数 [供应商](#供应商) 购买的硬件应预装最新固件。
可能需要更新旧硬件的固件，例如以支持 MAVLink 2。

可通过 _QGroundControl_ 更新无线电固件：[QGroundControl 用户指南 > 加载固件](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/firmware.html)。

## 高级设置/配置

开发部分提供了 [附加信息](../data_links/sik_radio.md) 关于固件构建和基于 AT 命令的配置。
非开发者通常不需要这些操作。