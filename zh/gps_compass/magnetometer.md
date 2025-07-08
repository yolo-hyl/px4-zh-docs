# 磁力计（指南针）硬件与设置

PX4 通过磁力计（指南针）来确定机体相对于地磁的偏航角和航向角。

[Pixhawk 系列](../flight_controller/pixhawk_series.md)飞控以及其他许多飞控都包含[内部磁力计](#internal-compass)。该磁力计用于外部磁力计的自动旋转检测和自动驾驶仪的台架测试。除上述用途外不应使用，且在校准后若存在外部磁力计将自动禁用。

在大多数机体，尤其是小型机体上，我们推荐使用[尽可能远离电机/电调供电线路安装的组合 GPS + 指南针](../assembly/mount_gps_compass.md)——通常安装在支架或机翼上（固定翼）。虽然可以使用[独立的外部指南针模块](#stand-alone-compass-modules)（如下所列），但更常见的是使用[组合 GPS/指南针模块](#combined-gnss-compass-modules)。

磁力计可通过 I2C/SPI 总线（Pixhawk 的 `GPS1` 或 `GPS2` 接口）或 CAN 总线连接。若模块名称中未包含 "CAN"，则通常是 I2C/SPI 指南针。

最多可连接 4 个内部或外部磁力计，但系统只会使用其中一个作为航向源。系统会根据磁力计的_优先级_自动选择最佳指南针（外部磁力计优先级高于内部磁力计）。若主用指南针在飞行中失效，将切换至下一个可用磁力计；若在飞行前失效，则将禁止解锁。

## 支持的指南针

### 指南针型号

PX4 支持多种磁力计型号，包括：Bosch BMM 150 MEMS（通过 I2C 总线）、HMC5883 / HMC5983（I2C 或 SPI）、IST8310（I2C）、LIS3MDL（I2C 或 SPI）、RM3100 等。其他支持的磁力计型号及其总线可参考[模块参考：磁力计（驱动）](../modules/modules_driver_magnetometer.md)中的驱动列表。

这些磁力计包含在独立指南针模块、组合指南针/GNSS 模块中，也集成在许多飞控中。

### 组合 GNSS/指南针模块

详见[全球导航卫星系统（GNSS）](../gps_compass/index.md#supported-gnss)获取适配模块列表。

::: info
若需要 GNSS 功能，则优先选择组合 GNSS/指南针模块而非下述独立模块。
:::

### 独立指南针模块

以下列表为不含 GNSS 的独立磁力计模块。

| 设备                                                                                                           | 指南针 | DroneCan |
| :--------------------------------------------------------------------------------------------------------------- | :-----: | :------: |
| [Avionics Anonymous UAVCAN 磁力计](https://www.tindie.com/products/avionicsanonymous/uavcan-magnetometer/)       |    ?    |          |
| [Holybro DroneCAN RM3100 指南针/磁力计](https://holybro.com/products/dronecan-rm3100-compass)                   | RM3100  |    ✓     |
| [RaccoonLab DroneCAN/Cyphal RM3100 磁力计](https://holybro.com/products/dronecan-rm3100-compass)                 | RM3100  |    ✓     |

说明：

- ✓ 或具体型号表示支持该功能，✘ 或空白表示不支持，"?" 表示未知。
- 非 "DroneCAN" 的指南针可默认为 SPI 或 I2C 接口。

### 内部磁力计

内部磁力计不建议作为航向源使用，因为其性能通常非常差。这在小型机体上尤为明显，因为飞控需靠近电机/电调供电线路，而这些线路会产生电磁干扰。尽管在大型机体（如 VTOL）中通过将飞控远离供电线路可减少干扰，但外部磁力计的性能仍显著优于内部磁力计。

::: tip
理论上若无外部磁力计可使用内部磁力计，但仅在 [EKF2_MAG_TYPE_INIT = Init (`6`)](../advanced_config/parameter_reference.md#EKF2_MAG_TYPE) 参数下且解锁前测量数据大致正常时才可使用。
:::

若存在外部磁力计，内部磁力计默认禁用。

## 安装

[指南针安装](../assembly/mount_gps_compass.md)说明了如何安装指南针或 GPS/指南针模块。

## I2C/SPI 指南针设置

在 [Pixhawk 系列](../flight_controller/pixhawk_series.md)飞控上，可通过 `GPS1` 或 `GPS2` 接口连接（均支持 I2C/SPI 接口）。无需其他配置。

<!-- 对于未遵循 Pixhawk 接口标准的飞控，需连接至 I2C/SPI 接口。 -->

## CAN 指南针设置

[DroneCAN](../dronecan/index.md) 介绍了 DroneCAN 外设（包括指南针）的设置方法。需将指南针连接至 [CAN 总线](../can/index.md#wiring)，启用 DroneCAN 并特别启用磁力计（搜索 `UAVCAN_SUB_MAG`）。

## 校准

[指南针校准](../config/compass.md) 说明了如何校准机体上的所有指南针。校准过程简单，系统将自动检测、[设置默认旋转](../advanced_config/parameter_reference.md#SENS_MAG_AUTOROT)、校准并优先使用所有连接的磁力计。

## 参见

- [指南针电源补偿](../advanced_config/compass_power_compensation.md)