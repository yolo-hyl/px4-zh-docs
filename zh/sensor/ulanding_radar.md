# Ainstein US-D1 标准雷达高度计

:::tip
此产品取代了_Aerotenna uLanding Radar_（已停产），但使用相同的驱动/设置。
:::

_Ainstein_ [US-D1 标准雷达高度计](https://ainstein.ai/us-d1-all-weather-radar-altimeter/) 是一款专为无人机优化的紧凑型微波测距仪。其探测范围约为 50 米，适用于地形跟随、精确悬停（如摄影）、防撞感应等应用场景。该产品的显著优势在于其可在所有天气条件和所有地形类型（包括水域）下稳定工作。用户手册请参考 [此处](https://ainstein.ai/wp-content/uploads/US-D1-Technical-User-Manual-D00.02.05.docx.pdf)。

![Ainstein US-DA](../../assets/hardware/sensors/ainstein/us_d1_hero.jpg)

该测距仪默认不包含在多数固件中，因此无法仅通过 _QGroundControl_ 设置参数直接使用（与部分其他测距仪不同）。若要使用它，需要将驱动添加到固件中，并更新配置文件以在启动时加载驱动。下文将说明具体操作。

## 硬件设置

该测距仪支持运行 NuttX 或 Posix 系统并提供串口接口的硬件。至少包括所有（或几乎所有）[Pixhawk 系列](../flight_controller/pixhawk_series.md)飞控器。

US-D1 可连接至任何未使用的 _串口_（UART），例如：TELEM2、TELEM3、GPS2 等。

## 参数设置

通过 [SENS_ULAND_CFG](../advanced_config/parameter_reference.md#SENS_ULAND_CFG) 配置激光雷达将运行的串口。无需手动设置该串口的波特率，因为驱动会自动配置。

::: info

如果 _QGroundControl_ 中未显示该配置参数，则可能需要 [将驱动添加到固件](../peripherals/serial_configuration.md#parameter_not_in_firmware)：

```plain
CONFIG_DRIVERS_DISTANCE_SENSOR_ULANDING_RADAR=y
```

:::