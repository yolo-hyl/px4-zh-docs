# I2C总线（开发概述）

I2C是一种分组交换串行通信协议，每个连接仅需两根线即可实现多个主设备与多个从设备的连接。  
该协议主要用于短距离板内通信中，将低速外围集成电路连接到处理器和微控制器。

Pixhawk/PX4支持其用于：
* 连接需要比严格串行UART更高数据速率的外部组件（如测距仪）。
* 与仅支持I2C的外围设备兼容。
* 允许多个设备连接到单一总线（节省端口资源）。  
  例如，LED、指南针、测距仪等。

::: info
该页面[硬件 > I2C 外设](../sensor_bus/i2c_general.md)包含如何使用（而非集成）I2C 外设以及解决常见设置问题的信息。
:::

:::tip
IMUs（加速度计/陀螺仪）不应通过I2C连接（通常使用[SPI](https://en.wikipedia.org/wiki/Serial_Peripheral_Interface_Bus)总线）。  
即使仅连接单个设备，总线速度也不足以进行振动过滤（例如），且每增加一个设备性能会进一步下降。
:::

## 集成I2C设备

驱动程序应`#include <drivers/device/i2c.h>`，然后为目标硬件（例如NuttX [here](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/drivers/device/nuttx/I2C.hpp)) 提供**I2C.hpp**中定义的抽象基类`I2C`的实现。

少量驱动程序还需要在[/src/drivers/](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers)中包含其设备类型的头文件（**drv_*.h**），例如[drv_led.h](https://github.com/PX4/PX4-Autopilot/blob/main/src/drivers/drv_led.h)。

要将驱动包含在固件中，必须将其添加到与目标对应的板级cmake文件中。  
您可以为单个驱动执行此操作：
```
CONFIG_DRIVERS_DISTANCE_SENSOR_LIGHTWARE_LASER_I2C=y
```

您也可以包含特定类型的所有驱动：
```
CONFIG_COMMON_DISTANCE_SENSOR=y
```

:::tip
例如，您可以在[px4_fmu-v4_default](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v4/default.px4board)配置中搜索/查看`CONFIG_DRIVERS_DISTANCE_SENSOR_LIGHTWARE_LASER_I2C`。
:::

## I2C驱动示例

要查找I2C驱动示例，请在[/src/drivers/](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers)中搜索**i2c.h**。

仅举几个示例：
* [drivers/distance_sensor/lightware_laser_i2c](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/lightware_laser_i2c) - [Lightware SF1XX LIDAR](../sensor/sfxx_lidar.md)的I2C驱动。
* [drivers/distance_sensor/lightware_laser_serial](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/distance_sensor/lightware_laser_serial) - [Lightware SF1XX LIDAR](../sensor/sfxx_lidar.md)的串行驱动。
* [drivers/ms5611](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer/ms5611) - 通过I2C（或SPI）连接的MS5611和MS6507气压传感器的I2C驱动。

## 进一步信息

* [I2C](https://en.wikipedia.org/wiki/I%C2%B2C) (Wikipedia)
* [I2C Comparative Overview](https://learn.sparkfun.com/tutorials/i2c) (learn.sparkfun.com)
* [Driver Framework](../middleware/drivers.md)