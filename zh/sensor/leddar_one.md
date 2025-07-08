# LeddarOne Lidar

[LeddarOne](https://leddartech.com/solutions/leddarone/) 是一款小型激光雷达模块，其具有窄但扩散的光束，提供出色的检测范围和性能，具有坚固、可靠且经济高效的封装。

其探测范围为 1cm 到 40 米，需要连接到 UART/串口总线。

<img src="../../assets/hardware/sensors/leddar_one.jpg" alt="LeddarOne Lidar 测距仪" width="200px" />

## 硬件设置

LeddarOne 可连接到任意未使用的 _串口_ (UART)，例如：TELEM2、TELEM3、GPS2 等。

根据您的板卡引脚定义和 LeddarOne 引脚定义制作线缆（如下所示）。只需连接 5V、TX、RX 和 GND 引脚。

| 引脚 | LeddarOne |
| --- | --------- |
| 1   | GND       |
| 2   | -         |
| 3   | VCC       |
| 4   | RX        |
| 5   | TX        |
| 6   | -         |

## 参数设置

使用 [SENS_LEDDAR1_CFG](../advanced_config/parameter_reference.md#SENS_LEDDAR1_CFG) 配置激光雷达运行的串口。[配置串口](../peripherals/serial_configuration.md)无需设置波特率，因为驱动程序会自动配置。

::: info
如果 _QGroundControl_ 中未显示该配置参数，可能需要 [将驱动添加到固件](../peripherals/serial_configuration.md#parameter_not_in_firmware)：

```plain
CONFIG_DRIVERS_DISTANCE_SENSOR_LEDDAR_ONE=y
```

:::

## 更多信息

- [LeddarOne 产品规格书](https://leddartech.com/app/uploads/dlm_uploads/2021/04/Spec-Sheet_LeddarOne_V10.0_EN-1.pdf)