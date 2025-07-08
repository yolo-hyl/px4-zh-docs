# TeraRanger 测距仪

TeraRanger 提供基于红外飞行时间（ToF）技术的轻量级距离测量传感器。  
它们通常比声呐更快，测量范围更广，且比激光系统更小更轻。

PX4 支持以下型号：

- [TeraRanger Evo 60m](https://www.terabee.com/shop/lidar-tof-range-finders/teraranger-evo-60m/) (0.5 – 60 m)  
- [TeraRanger Evo 600Hz](https://www.terabee.com/shop/lidar-tof-range-finders/teraranger-evo-600hz/) (0.75 - 8 m)  

::: info  
PX4 同时支持 _TeraRanger One_（需使用 I2C 适配器）。  
该产品已停产。  
:::  

## 购买渠道  

- TBD  

## 引脚分配  

## 接线  

所有 TeraRanger 传感器必须通过 I2C 总线连接。  

## 软件配置  

通过参数 [SENS_EN_TRANGER](../advanced_config/parameter_reference.md#SENS_EN_TRANGER) 启用传感器（可设置传感器类型或让 PX4 自动检测类型）。  

::: info  
若对 Evo 传感器使用自动检测功能，测距范围的最小值和最大值将设置为整个 Evo 系列的极值（当前为 0.5 - 60 m）。  
为使用正确的最大/最小值，应在参数中指定对应的 Evo 传感器型号（而非使用自动检测）。  
:::  

:::tip  
该测距仪的驱动通常包含在固件中。若缺失，还需将驱动 (`distance_sensor/teraranger`) 添加到板级配置中。  
:::  

## 更多信息  

- [模块参考：距离传感器（驱动）: teraranger](../modules/modules_driver_distance_sensor.md#teraranger)