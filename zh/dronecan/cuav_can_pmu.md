# CAUV CAN PMU

CAN PMU<sup>&reg;</sup> 是 CUAV<sup>&reg;</sup> 开发的高精度 [DroneCAN](index.md) 电源模块。  
它运行 CUAV ITT 补偿算法，使无人机能够更精确地获取电池数据。

![CAN PMU](../../assets/hardware/power_module/cuav_can/can_pmu.jpg)

建议在大型商业机体上使用，也可用于研究机体。

## 购买渠道

- [CUAV 店铺](https://store.cuav.net/index.php)
- [CUAV 速卖通](https://www.aliexpress.com/item/4000369700535.html)

## 硬件规格

- **处理器**: STM32F412
- **电压输入**: 6~62V(2-15S)
- **最大电流**: 110A
- **电压精度**: ±0.05V
- **电流精度**: ±0.1A
- **分辨率**: 0.01A/V
- **最大输出功率**: 6000W/90S
- **最大稳定功率**: 5000W
- **电源端口输出**: 5.4V/5A
- **工作温度**: -20~+100
- **固件升级**: 支持
- **校准**: 无需校准
- **接口类型**:
  - **输入/输出**: XT90(线缆)/Amass 8.0(模块)
  - **电源端口**: 5025850670
  - **CAN**: GHR-04V-S
- **外观**:
  - **尺寸**: 46.5mm * 38.5mm * 22.5mm
  - **重量**: 76g

## 硬件安装

### 包装内容

![CAN PMU 列表](../../assets/hardware/power_module/cuav_can/can_pmu_list.png)

### 引脚定义

![CAN PMU](../../assets/hardware/power_module/cuav_can/can_pmu_pinouts_en.png)

![CAN PMU](../../assets/hardware/power_module/cuav_can/can_pmu_pinouts_en2.png)

## 接线

![CAN PMU](../../assets/hardware/power_module/cuav_can/can_pmu_connection_en.png)

连接步骤：

- 连接飞控的 CAN1/2 接口与模块的 CAN 接口。
- 将 V5 系列电源线连接到 V5 飞控的 Power2 接口（如果其他飞控连接到 Power 接口），并连接到模块的 Power 接口。

## 飞控配置

在 _QGroundControl_ [机体设置 > 参数](../advanced_config/parameters.md) 中设置以下参数并重启：

- [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE): 设置为: _Sensors Automatic Config_

  ![qgc 设置](../../assets/hardware/power_module/cuav_can/qgc_set_en.png)

- [UAVCAN_SUB_BAT](../advanced_config/parameter_reference.md#UAVCAN_SUB_BAT): 设置为: _Raw data_

  ![QGC - 设置 UAVCAN_SUB_BAT 参数为原始数据](../../assets/hardware/power_module/cuav_can/qgc_set_usavcan_sub_bat.png)

## 更多信息

[CAN PMU 手册](http://manual.cuav.net/power-module/CAN-PMU.pdf)

[CAN PMU 电源检测模块 > 启用 CAN PMU > PX4 固件](http://doc.cuav.net/power-module/can-pmu/en/) (CUAV 文档)