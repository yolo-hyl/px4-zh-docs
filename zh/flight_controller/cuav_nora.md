# CUAV Nora飞控

:::warning
PX4不生产此（或任何）自动驾驶仪。
如需硬件支持或合规问题，请联系[制造商](https://www.cuav.net)。
:::

[Nora](https://doc.cuav.net/flight-controller/x7/en/nora.html)<sup>&reg;</sup>飞控是一款高性能自动驾驶仪。
它是工业无人机和大中型重载无人机的理想选择。
主要面向商业厂商供货。

![CUAV x7](../../assets/flight_controller/cuav_nora/nora.png)

Nora是CUAV X7的变种版本。
采用集成主板（软硬板一体化），减少了飞控内部连接器，提升了可靠性，并将所有接口布局在侧面（使布线更简洁）。

::: info
该飞控获得[厂商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 特性

- 内部减震设计
- 集成化工艺降低接口损坏导致的故障率
- 支持USB_HS，日志下载更快（PX4尚未支持）
- 支持更多dshot输出
- 支持IMU加热，提升传感器性能
- 专用CAN电池接口
- 3组IMU传感器
- 车规级RM3100指南针
- 高性能处理器

:::tip
厂商[CUAV Docs](https://doc.cuav.net/flight-controller/x7/en/nora.html)是Nora的权威参考。
建议优先使用，因其包含最完整和最新的信息。
:::

## 快速概览

- 主FMU处理器：STM32H743
- 板载传感器：

  - 加速度计/陀螺仪：ICM-20689
  - 加速度计/陀螺仪：ICM-20649
  - 加速度计/陀螺仪：BMI088
  - 磁强计：RM3100
  - 气压计：MS5611\*2

- 接口：
  - 14路PWM输出（12路支持Dshot）
  - 支持多种遥控输入（SBUs / CPPM / DSM）
  - 模拟/PWM RSSI输入
  - 2个GPS端口（GPS和UART4端口）
  - 4组I2C总线（含2组专用I2C端口）
  - 2个CAN总线端口
  - 2个电源接口（Power A为通用ADC接口，Power C为DroneCAN电池接口）
  - 2路ADC输入
  - 1个USB接口
- 电源系统：
  - 供电：4.3~5.4V
  - USB输入：4.75~5.25V
  - 伺服电源输入：0~36V
- 重量与尺寸：
  - 重量：101 g
- 其他特性：
  - 工作温度：-20 ~ 80°c（实测值）
  - 三重IMU
  - 支持温度补偿
  - 内部减震

::: info
运行PX4固件时，仅8路PWM输出可用。
其余6路PWM端口仍在适配中（因此当前不兼容VOLT）。
:::

## 购买渠道

- [CUAV Store](https://store.cuav.net)<\br>
- [CUAV Aliexpress](https://www.aliexpress.com/item/4001042501927.html?gps-id=8041884&scm=1007.14677.110221.0&scm_id=1007.14677.110221.0&scm-url=1007.14677.110221.0&pvid=3dc0a3ba-fa82-43d2-b0b3-6280e4329cef&spm=a2g0o.store_home.promoteRecommendProducts_7913969.58)

## 接线说明

[CUAV nora接线快速指南](https://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-nora.html)

## 尺寸与引脚定义

![CUAV x7](../../assets/flight_controller/cuav_nora/nora-size.jpg)

![X7引脚定义](../../assets/flight_controller/cuav_nora/nora-pinouts.jpg)

:::warning
`RCIN`端口仅限为接收机供电，不能连接任何负载/电源。
:::

## 电压等级

Nora AutoPilot\*可通过三个电源实现三冗余供电。供电轨包括：**POWERA**、**POWERC**和**USB**。

::: info
输出供电轨 **PWM OUT**（0V至36V）不为飞控板供电（也不由其供电）。
必须为**POWERA**、**POWERC**或**USB**中的任意一个供电，否则板载设备将断电。
:::

**正常运行最大电压等级**

在此条件下系统将按以下顺序使用所有电源供电：

1. **POWERA**和**POWERC**输入（4.3V至5.4V）
2. **USB**输入（4.75V至5.25V）

## 固件编译

:::tip
大多数用户无需编译此固件！
当连接合适硬件时，_QGroundControl_ 会自动预编译并安装。
:::

要为该目标[编译PX4](../dev_setup/building_px4.md)：

```
make cuav_nora_default
```

## 支持的平台/机体

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/车/船。
完整支持的配置列表可见[机体参考](../airframes/airframe_reference.md)。

## 更多信息

- [快速指南](https://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-nora.html)
- [CUAV文档](http://doc.cuav.net)
- [nora原理图](https://github.com/cuav/hardware/tree/master/X7_Autopilot)