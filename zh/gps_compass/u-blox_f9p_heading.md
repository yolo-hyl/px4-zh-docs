# 使用双u-blox F9P的RTK GPS航向角

机体上安装两个u-blox F9P [RTK GPS](../gps_compass/rtk_gps.md)模块可以精确计算航向角（即作为罗盘航向估计的替代方案）。在此场景中，两个GPS设备被称为_Moving Base_（移动基站）和_Rover_（流动站）。

## 支持的设备

该功能适用于支持CAN或提供GPS UART2端口的F9P设备。以下设备已验证支持：

- [ARK RTK GPS](https://arkelectron.com/product/ark-rtk-gps/) (arkelectron.com)
- [SparkFun GPS-RTK2 Board - ZED-F9P](https://www.sparkfun.com/products/15136) (www.sparkfun.com)
- [SIRIUS RTK GNSS ROVER (F9P)](https://store-drotek.com/911-sirius-rtk-gnss-rover-f9p.html) (store-drotek.com)
- [mRo u-blox ZED-F9 RTK L1/L2 GPS](https://store.mrobotics.io/product-p/m10020d.htm) (store.mrobotics.io)
- [Holybro H-RTK F9P Helical or Base](https://holybro.com/products/h-rtk-f9p-gnss-series) (Holybro Store)
- [Holybro DroneCAN H-RTK F9P Rover or Helical](https://holybro.com/collections/dronecan-h-rtk) (Holybro Store)
- [CUAV C-RTK 9Ps](https://store.cuav.net/shop/c-rtk-9ps/) (CUAV Store)

::: info

- [Freefly RTK GPS](../gps_compass/rtk_gps_freefly.md) 和 [Holybro H-RTK F9P Rover Lite](../gps_compass/rtk_gps_holybro_h-rtk-f9p.md) 无法使用，因为它们未提供CAN或UART2端口。
- 支持的设备列表也可在 [RTK GNSS (GPS) > 支持的设备](../gps_compass/rtk_gps.md#supported-devices) 中查看。
  :::

## 配置步骤

理想情况下，两个天线应完全相同，处于同一水平面且朝向一致，并具有相同大小和形状的接地平面 ([应用说明](https://content.u-blox.com/sites/default/files/documents/ZED-F9P-MovingBase_AppNote_UBX-19009093.pdf), _System Level Considerations_ 部分)。

- 应用说明未明确模块的最小间距要求（PX4测试中使用过50cm）
- 天线可根据需求布置，但必须配置 [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)：
  [RTK GPS > GPS作为偏航角/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source)

### UART配置

- GPS设备的UART2需要互联（"Moving Base"的TXD2连接到 "Rover"的RXD2）
- 将每个GPS的UART1连接到飞控的独立UART端口，并配置为GPS模式（波特率设为`Auto`）
  映射关系如下：
  - 主GPS = Rover
  - 从GPS = Moving Base
- 设置 [GPS_UBX_MODE](../advanced_config/parameter_reference.md#GPS_UBX_MODE) 为 `Heading`（1）
- [EKF2_GPS_CTRL](../advanced_config/parameter_reference.md#EKF2_GPS_CTRL) 参数的bit 3必须设置（参见 [RTK GPS > GPS作为偏航角/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source)）
- 可能需要设置 [GPS_YAW_OFFSET](../advanced_config/parameter_reference.md#GPS_YAW_OFFSET)（参见 [RTK GPS > GPS作为偏航角/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source)）
- 重启并等待两个设备获取GPS信号
  `gps status` 应显示主GPS进入RTK模式，表示航向角已可用

### CAN配置

请参考各设备的CAN RTK GPS文档进行配置（如 [ARK RTK GPS > 配置移动基线与GPS航向](../dronecan/ark_rtk_gps.md#setting-up-moving-baseline-gps-heading)）

::: info
使用固定基站的RTK时，从GPS将显示相对于基站的RTK状态。
:::

## 进一步信息

- [ZED-F9P移动基线应用（应用说明）](https://content.u-blox.com/sites/default/files/documents/ZED-F9P-MovingBase_AppNote_UBX-19009093.pdf) - 通用配置指南
- [RTK GPS > GPS作为偏航角/航向源](../gps_compass/rtk_gps.md#configuring-gps-as-yaw-heading-source)