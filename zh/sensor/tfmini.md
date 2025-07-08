# Benewake TFmini LiDAR

_Benewake TFmini LiDAR_ 是一款体积小巧、成本低、功耗低的LiDAR，测距范围为12米。

PX4 支持该系列的三个变种：TFmini-s、TFmini-i、TFmini Plus。
这些设备必须连接到 UART/串口总线。

![TFmini LiDAR](../../assets/hardware/sensors/tfmini/tfmini_hero.jpg)

## 购买渠道

- [TFmini-s](https://en.benewake.com/TFminiS/index_proid_325.html)
- [TFmini-i](https://en.benewake.com/TFminii/index_proid_324.html) (工业级)
- [TFmini Plus](https://en.benewake.com/TFminiPlus/index_proid_323.html)

## 硬件设置

TFmini 可连接到任意未使用的 _串口_（UART），例如：`TELEM2`、`TELEM3`、`GPS2` 等。

## 参数配置

使用 [SENS_TFMINI_CFG](../advanced_config/parameter_reference.md#SENS_TFMINI_CFG) 配置 LiDAR 运行的串口。
无需设置波特率（该参数在传感器驱动中已硬编码，仅支持一种速率）。

::: info
如果配置参数在 _QGroundControl_ 中不可用，则可能固件中未包含 [tfmini](../modules/modules_driver_distance_sensor.md#tfmini) 驱动。
关于如何添加的说明请参见：

- [串口配置：QGroundControl 中缺少配置参数](../peripherals/serial_configuration.md#parameter_not_in_firmware)
- [PX4 板级配置 (Kconfig)](../hardware/porting_guide_config.md#px4-menuconfig-setup)

:::