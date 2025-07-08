# 空速传感器

空速传感器对于固定翼和垂直起降（VTOL）机体是*高度推荐的*。  
这是因为空速传感器是自动控制系统检测失速的唯一手段。对于固定翼飞行，保证升力的是空速而非地速！

![数字空速传感器](../../assets/hardware/sensors/airspeed/digital_airspeed_sensor.jpg)

## 硬件选项

推荐的数字空速传感器包括：

- 基于[皮托管](https://en.wikipedia.org/wiki/Pitot_tube)
  - I2C MEAS Spec系列（例如[MS4525DO](https://www.te.com/usa-en/product-CAT-BLPS0002.html)，[MS5525](https://www.te.com/usa-en/product-CAT-BLPS0003.html))
    - [mRo I2C 空速传感器 JST-GH MS4525DO](https://store.3dr.com/mro-i2c-airspeed-sensor-jst-gh-ms4525do/) （3DR商店）  
    - [数字差分空速传感器套件 - MS4525DO](https://store-drotek.com/793-digital-differential-airspeed-sensor-kit-.html) （Drotek）  
    - [Holybro 数字空速传感器 - MS4525DO](https://holybro.com/collections/sensors/products/digital-air-speed-sensor-ms4525do)  
    - [Holybro 数字空速传感器 - MS5525DSO](https://holybro.com/collections/sensors/products/digital-air-speed-sensor-ms5525dso)  
  - I2C Sensirion系列（例如SDP33）  
    - [ThunderFly TFPITOT01 轻量级皮托管](https://docs.thunderfly.cz/avionics/TFPITOT01/)  
    - [Drotek SDP3x 空速传感器套件](https://store-drotek.com/848-sdp3x-airspeed-sensor-kit-sdp33.html)  
  - DroneCAN接口  
    - [Holybro 高精度DroneCAN空速传感器 - DLVR](https://holybro.com/collections/sensors/products/high-precision-dronecan-airspeed-sensor-dlvr)  
    - [RaccoonLab Cyphal/CAN 和 DroneCAN 空速传感器 - MS4525DO](https://raccoonlab.co/tproduct/360882105-652259850171-cyphal-and-dronecan-airspeed-v2)  
    - [Avionics Anonymous 空气数据计算机配OAT探头](https://www.tindie.com/products/avionicsanonymous/uavcan-air-data-computer-airspeed-sensor/)  
- 基于[文丘里效应](https://en.wikipedia.org/wiki/Venturi_effect)
  - [TFSLOT](airspeed_tfslot.md) 文丘里效应空速传感器。

## 配置

### 启用空速传感器

空速传感器驱动不会自动启动。通过对应参数启用每种传感器类型：

- **Sensirion SDP3X:** [SENS_EN_SDP3X](../advanced_config/parameter_reference.md#SENS_EN_SDP3X)  
- **TE MS4525:** [SENS_EN_MS4525DO](../advanced_config/parameter_reference.md#SENS_EN_MS4525DO)  
- **TE MS5525:** [SENS_EN_MS5525DS](../advanced_config/parameter_reference.md#SENS_EN_MS5525DS)  
- **Eagle Tree 空速传感器:** [SENS_EN_ETSASPD](../advanced_config/parameter_reference.md#SENS_EN_ETSASPD)  

还需确认 [ASPD_PRIMARY](../advanced_config/parameter_reference.md#ASPD_PRIMARY) 为 `1`（见下一部分，默认值）。

### 多个空速传感器

:::warning 实验性  
使用多个空速传感器为实验性功能。  
:::

若有多个空速传感器，可通过 [ASPD_PRIMARY](../advanced_config/parameter_reference.md#ASPD_PRIMARY) 选择优先使用的主传感器，其中 `1`、`2`、`3` 表示传感器启动顺序：

- `0`: 合成空速估算（地速减去风速）  
- `1`: 第一个启动的空速传感器（默认）  
- `2`: 第二个启动的空速传感器  
- `3`: 第三个启动的空速传感器  

空速选择器会首先验证指定传感器，仅在指定传感器通过空速检查失败时才会回退到其他传感器（[ASPD_DO_CHECKS](../advanced_config/parameter_reference.md#ASPD_DO_CHECKS) 用于配置检查）。

选定的传感器随后用于[向估计器（EKF2）提供数据](../advanced_config/tuning_the_ecl_ekf.md#airspeed)和控制器。

### 传感器特定配置

除启用传感器外，通常不需要其他特定配置。若需要，应参考对应传感器页面（例如 [TFSLOT > 配置](airspeed_tfslot.md#configuration)）。

未单独页面的传感器特定配置如下：

- **Sensirion SDP3X:** [CAL_AIR_CMODEL](../advanced_config/parameter_reference.md#CAL_AIR_CMODEL)（提供所需设置概述），[CAL_AIR_TUBED_MM](../advanced_config/parameter_reference.md#CAL_AIR_TUBED_MM)，[CAL_AIR_TUBELEN](../advanced_config/parameter_reference.md#CAL_AIR_TUBELEN)。

## 校准

请按照以下步骤校准空速传感器：[基础配置 > 空速](../config/airspeed.md)。

## 参见

- [使用PX4的导航滤波器（EKF2）> 空速](../advanced_config/tuning_the_ecl_ekf.md#airspeed)  
- [空速驱动](https://github.com/PX4/PX4-Autopilot/tree/main/src/drivers/differential_pressure)（源代码）  
- [无空速传感器的VTOL](../config_vtol/vtol_without_airspeed_sensor.md)