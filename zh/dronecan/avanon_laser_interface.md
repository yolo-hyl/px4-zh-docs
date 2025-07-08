# Avionics Anonymous 激光高度计 DroneCAN 接口

::: info
2022 年，UAVCAN（v0）被分叉并作为 `DroneCAN` 维护。
虽然该产品仍提及 "UAVCAN"，但与 PX4 的 DroneCAN 支持完全兼容。
:::

[Avionics Anonymous 激光高度计接口](https://www.tindie.com/products/avionicsanonymous/uavcan-laser-altimeter-interface/) 允许通过 CAN 总线连接[多种常见测距仪](#supported_rangefinders)（这是比 I2C 更稳健的接口）。

![Avionics Anonymous 激光高度计 DroneCAN 接口](../../assets/hardware/sensors/avionics_anon_uavcan_alt_interface/avionics_anon_altimeter_uavcan_interface.jpg)

## 购买渠道

- [AvAnon 激光接口](https://www.tindie.com/products/avionicsanonymous/uavcan-laser-altimeter-interface/)

<a id="supported_rangefinders"></a>

## 支持的测距仪

完整支持的测距仪列表请参考上方链接。

撰写时支持的测距仪包括：

- Lightware SF30/D
- Lightware SF10/a
- Lightware SF10/b
- Lightware SF10/c
- Lightware SF11/c
- Lightware SF/LW20/b
- Lightware SF/LW20/c

## 硬件设置

### 接线

测距仪（激光器）连接到 AvAnon 接口板，该板连接到飞控的某个 CAN 接口。  
接线方式参照上文引脚定义，或可购买配套线缆直接连接系统。  
配套线缆购买链接请见 [此处](https://www.tindie.com/products/avionicsanonymous/uavcan-laser-altimeter-interface/)。

接口板为激光器提供滤波电源输出，但不提供独立电压调节。因此激光器必须兼容板载供电电压。

### 引脚定义

### CAN 接口

| 引脚 | 名称      | 描述                                                                                             |
| ---- | --------- | ------------------------------------------------------------------------------------------------ |
| 1    | POWER_IN  | 电源输入。支持 4.0-5.5V，但必须与连接的激光器兼容。                                               |
| 2    | TX/SCL    | 串口模式下为 TX，I2C 模式下为时钟。                                                              |
| 3    | RX/SDA    | 串口模式下为 RX，I2C 模式下为数据。                                                              |
| 4    | GND       | 信号/电源地。                                                                                   |

### 激光接口

| 引脚 | 名称       | 描述                              |
| ---- | ---------- | --------------------------------- |
| 1    | POWER_OUT  | 滤波后的供电电压输出。            |
| 2    | CAN+       | 串口模式下为 TX，I2C 模式下为时钟。|
| 3    | RX/SDA     | 串口模式下为 RX，I2C 模式下为数据。|
| 4    | GND        | 信号/电源地。                     |

## PX4 配置

要启用激光高度计，需在 QGroundControl 中设置以下参数：

- 通过设置 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 为非零值启用 DroneCAN。
- 通过设置 [UAVCAN_SUB_RNG](../advanced_config/parameter_reference.md#UAVCAN_SUB_RNG) 启用 DroneCAN 测距仪订阅。
- 使用 [UAVCAN_RNG_MIN](../advanced_config/parameter_reference.md#UAVCAN_RNG_MIN) 和 [UAVCAN_RNG_MAX](../advanced_config/parameter_reference.md#UAVCAN_RNG_MAX) 设置测距仪的最小和最大量程。