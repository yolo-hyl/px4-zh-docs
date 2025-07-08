# Lanbao PSK-CM8JL65-CC5 ToF 红外测距传感器（已停产）

<Badge type="info" text="Discontinued" />

:::warning
该产品已停产，不再提供商业销售。
:::

[Lanbao PSK-CM8JL65-CC5](https://www.seeedstudio.com/PSK-CM8JL65-CC5-Infrared-Distance-Measuring-Sensor-p-4028.html) 是一款小型红外测距传感器，测距范围为 0.17m-8m，具备毫米级分辨率。必须通过 UART/串行总线连接。

- 尺寸：38 mm x 18mm x 7mm
- 重量：≤10g

![PSK-CM8JL65-CC5 ToF IR Distance Sensor - Hero image](../../assets/hardware/sensors/cm8jl65/psk_cm8jl65_hero.jpg)

## 硬件配置

PSK-CM8JL65-CC5 可连接任意未使用的 _串口_，例如：`TELEM2`、`TELEM3`、`GPS2` 等。

传感器底部标有引脚定义：

![PSK-CM8JL65-CC5 ToF IR Distance Sensor - Pinout connections](../../assets/hardware/sensors/cm8jl65/psk-cm8jl65-cc5-02.jpg)

## 参数配置

使用 [SENS_CM8JL65_CFG](../advanced_config/parameter_reference.md#SENS_CM8JL65_CFG) 配置激光雷达运行的串口。

::: info
如果 _QGroundControl_ 中未显示该配置参数，可能需要 [将驱动添加到固件](../peripherals/serial_configuration.md#parameter_not_in_firmware)：

```plain
distance_sensor/cm8jl65
```

:::

若要将该传感器用于 _防撞_ 功能，还需设置参数 [SENS_CM8JL65_R_0](../advanced_config/parameter_reference.md#SENS_CM8JL65_R_0) 和 [CP_DIST](../advanced_config/parameter_reference.md#CP_DIST)。更多信息请参见：[防撞](../computer_vision/collision_prevention.md#rangefinder)。