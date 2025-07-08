# Holybro PM02D 电源模块

Holybro PM02D 数字电源模块为飞控和电源分配板提供稳定电源，并向自动驾驶仪发送电池电压和电流信息（包括供应给飞控和电机的电压和电流）。

电源模块通过 I2C 协议进行连接。  
该模块专为基于 Pixhawk FMUv5X 和 FMUv6X 开放标准的飞控设计，包括 [Pixhawk 5X](../flight_controller/pixhawk5x.md)。

::: info  
该电源模块 **不兼容** 需要模拟电源模块的飞控，包括：[Pixhawk 4](../flight_controller/pixhawk4.md)、[Durandal](../flight_controller/durandal.md)、[Pix32 v5](../flight_controller/holybro_pix32_v5.md) 等。  
:::

![PM02D](../../assets/hardware/power_module/holybro_pm02d/pm02d_hero.jpg)

## 规格参数

- **最大输入电压**：36V  
- **额定电流**：60A  
- **最大电流**：<60S 时为 120A  
- **最大电流检测**：164A  
- **支持电池**：最高 6S 电池  
- **通信协议**：I2C  
- **开关稳压器输出**：5.2V 和 3A 最大  
- **重量**：20g  
- **使用的 IC**：TI INA228  

## 包装内容

- 带 XT60 接口的 PM02D 板  
- 6 针 2.00mm 间距 CLIK-Mate 电缆（用于为飞控供电）  

## 购买渠道

[从 Holybro 商店订购](https://holybro.com/products/pm02d-power-module)

## 接线/连接

![pm02d_pinout](../../assets/hardware/power_module/holybro_pm02d/pm02d_pinout.png)

更多接线和连接信息请参阅：[Holybro Pixhawk 5x 接线快速入门](../assembly/quick_start_pixhawk5x.md)。

## PX4 配置

启用参数 [SENS_EN_INA228](../advanced_config/parameter_reference.md#SENS_EN_INA228)

::: warning  
该模块存在已停产的低压版本（6S），使用 TI INA226 IC。  
对于该模块，必须启用参数 [SENS_EN_INA226](../advanced_config/parameter_reference.md#SENS_EN_INA226)。  
:::

注意：在 [电池配置](../config/battery.md) 中不需要设置电流分压器和电压分压器（默认值在 5% 范围内准确）。

## 相关参考

- [数字电源模块（PM）设置](https://docs.holybro.com/power-module-and-pdb/power-module/digital-power-module-pm-setup#px4-setup)（厂商指南）