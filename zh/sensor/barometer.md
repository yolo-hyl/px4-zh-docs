# 气压计

气压计用于测量大气压力，在无人机中作为高度传感器使用。

大多数运行 PX4 的[飞控](../flight_controller/index.md)都包含气压计。默认情况下，PX4 会优先选择可用的最高优先级气压计，并将其配置为[高度估计](../advanced_config/tuning_the_ecl_ekf.md#height)的数据源。若检测到传感器故障，PX4 将自动切换到下一个最高优先级传感器。

通常气压计无需用户配置！

## 硬件选项

[Pixhawk standard](../flight_controller/autopilot_pixhawk_standard.md) 飞控包含气压计，其他[多种飞控](../flight_controller/index.md)也具备此功能。

其他硬件中也包含气压计：

- [CUAV NEO 3 Pro GNSS模块](https://doc.cuav.net/gps/neo-series-gnss/en/neo-3-pro.html#key-data) ([MS5611](../modules/modules_driver_baro.md#ms5611))
- [RaccoonLab L1 GNSS NEO-M8N](https://raccoonLab.co/tproduct/360882105-258620719271-cyphal-and-dronecan-gnss-m8n-magnetomete)

撰写本文时，支持的驱动/部件包括：bmp280, bmp388 (和 BMP380), dps310, goertek (spl06), invensense (icp10100, icp10111, icp101xx, icp201xx), lps22hb, lps25h, lps33hw, maiertek (mpc2520), mpl3115a2, ms5611, ms5837, tcbp001ta。

支持的气压计型号可通过[模块参考：Baro (驱动)](../modules/modules_driver_baro.md)文档中列出的驱动名称推断（驱动源码：[PX4-Autopilot/src/drivers/barometer](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer)）。

## PX4 配置

通常气压计无需用户配置。若需要：

- 可通过 [EKF2_BARO_CTRL](../advanced_config/parameter_reference.md#EKF2_BARO_CTRL) 参数启用/禁用气压计作为[高度估计](../advanced_config/tuning_the_ecl_ekf.md#height)的数据源。
- 可通过各气压计的 [CAL_BAROx_PRIO](../advanced_config/parameter_reference.md#CAL_BARO0_PRIO) 参数调整选择顺序。
- 通过将 [CAL_BAROx_PRIO](../advanced_config/parameter_reference.md#CAL_BARO0_PRIO) 值设为 `0` 可禁用气压计。

## 校准

气压计无需校准。

<!-- 注释：
- 绝对值并不重要，因为我们仅使用当前值与 EKF2 初始化时的值的差异。
- 通常存在比例因子误差，但会通过 EKF2 中的偏差估计器使用GNSS高度进行补偿（我们不提供校准方法）。只要无人机高度变化速度不过快（可能低于200-300km/h；暂无实际数据）此方法即可。
- 可通过参数 SENS_BARO_QNH（https://en.wikipedia.org/wiki/Altimeter_setting）校正气压计读数，但仅在飞行员需要绝对气压高度时才需调整。
-->

## 开发者信息

- [气压计驱动源码](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/barometer)
- [模块参考：Baro (驱动)](../modules/modules_driver_baro.md) 文档。