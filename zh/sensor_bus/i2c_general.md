# I2C总线外设

[I2C](https://en.wikipedia.org/wiki/I2C) 是一种串行通信协议，通常用于小型无人机上连接测距仪、LED、指南针等外围组件。

推荐用于以下场景：
* 连接需要低带宽和低延迟通信的离板组件，例如[测距仪](../sensor/rangefinders.md)、[磁力计](../gps_compass/magnetometer.md)、[空速传感器](../sensor/airspeed.md)和[转速计](../sensor/tachometers.md)。
* 与仅支持I2C的外围设备兼容。
* 允许多个设备连接到单个总线，有助于节省端口资源。

I2C通过每对连接仅使用2根线（SDA、SCL）即可让多个主设备连接多个从设备。理论上，一条总线可支持128个设备，每个设备通过其唯一地址访问。

::: info
在需要更高数据率或在较大机体上（传感器距离飞控较远）时，通常会优先选择UAVCAN。
:::

## 接线

I2C使用一对线：SDA（串行数据）和SCL（串行时钟）。总线为开漏类型，意味着设备通过接地数据线进行通信。它使用上拉电阻将其推至`log.1`（空闲状态）——通常每根线的上拉电阻位于总线终端设备上。一个总线可连接多个I2C设备，设备之间无需交叉连接。

根据Dronecode标准，4芯电缆配备JST-GH连接器。为确保通信可靠性和减少串扰，建议参考[电缆扭绞](../assembly/cable_wiring.md#i2c-cables)和上拉电阻放置的相关建议。

![Cable twisting](../../assets/hardware/cables/i2c_jst-gh_cable.jpg)

## 检查总线和设备状态

一个有用的总线分析工具是[i2cdetect](../modules/modules_command.md#i2cdetect)。该工具通过地址列出可用的I2C设备，可用于检测总线上的设备是否可用以及飞控能否与其通信。

该工具可通过以下命令在PX4终端中运行：

```
i2cdetect -b 1
```
其中`-b`参数后指定总线编号

## 常见问题

### 地址冲突

如果总线上有两个I2C设备具有相同ID，会发生冲突，导致设备无法正常工作。这通常发生在用户需要连接两个相同类型的传感器时，也可能因设备默认地址重复而发生。

某些I2C设备允许为其中一个设备选择新地址以避免冲突。部分设备不支持此功能或地址选择范围有限（无法避免冲突）。

如果无法更改地址，可考虑使用[I2C地址转换器](#i2c-address-translators)。

### 传输容量不足

随着更多设备的添加，每个设备的可用带宽通常会下降。具体下降幅度取决于每个设备的带宽使用情况。因此可以连接许多低带宽设备，如[转速计](../sensor/tachometers.md)。添加过多设备可能导致传输错误和网络不可靠。

减少该问题的方法包括：
* 将设备分组（每组设备数量相近），并连接到不同的飞控端口
* 提高总线速度限制（通常外部I2C总线设置为100kHz）

### 过大的线路电容

随着更多设备/线路的添加，总线线路的电容会增加。问题可以通过示波器检测，此时会发现SDA/SCL信号的边沿不再锐利。

减少该问题的方法包括：
* 将设备分组（每组设备数量相近），并连接到不同的飞控端口
* 使用更短且更高质量的I2C电缆（详见[cable wiring page](../assembly/cable_wiring.md#i2c-cables)）
* 通过使用[I2C总线加速器](#i2c-bus-accelerators)将设备分成小总线，采用弱开漏驱动器降低电容

## I2C总线加速器

I2C总线加速器是独立电路，可用于支持I2C总线上的更长线路。它们通过物理方式将I2C网络分为两部分，并使用自己的晶体管放大I2C信号。

可用的加速器包括：
- [Thunderfly TFI2CEXT01](https://docs.thunderfly.cz/avionics/TFI2CEXT01/):
  ![I2C总线扩展器](../../assets/peripherals/i2c_tfi2cext/tfi2cext01a_bottom.jpg)
  - 该设备配备Dronecode连接器，因此很容易添加到Pixhawk I2C设置中。
  - 该模块无需设置（开箱即用）。

### I2C电平转换器功能

部分I2C设备的数据线为5V，而Pixhawk连接器标准端口期望这些线路为3.3V。可以使用TFI2CEXT01作为电平转换器将5V设备连接到Pixhawk I2C端口。此功能可行是因为TFI2CEXT01的SCL和SDA线路具有5V耐受性。

## I2C地址转换器

I2C地址转换器可用于在无法分配唯一地址的系统中防止I2C地址冲突。它们通过监听I2C通信并在调用从设备时根据预设算法转换地址来工作。

支持的I2C地址转换器包括：
- [Thunderfly TFI2CADT01](../sensor_bus/translator_tfi2cadt.md)
  - 该设备配备Dronecode连接器，因此很容易添加到Pixhawk I2C设置中。

## I2C总线分路器

I2C总线分路器是将飞控上的I2C端口拆分为多个连接器的设备。如果您想在只有一个I2C端口（或端口过少）的飞控上使用多个I2C外设（如空速传感器和距离传感器），这会非常有用。[I2C地址转换器](../sensor_bus/translator_tfi2cadt.md)和[I2C总线加速器](#i2c-bus-accelerators)也可实现此功能。

## 开发者参考

### 内部链接
- [测距仪](../sensor/rangefinders.md)
- [磁力计](../gps_compass/magnetometer.md)
- [空速传感器](../sensor/airspeed.md)
- [转速计](../sensor/tachometers.md)

### 外部链接
- [I2C协议](https://en.wikipedia.org/wiki/I2C)

### 代码示例
```bash
# 检测I2C设备
i2cdetect -y 1
```

### 相关硬件
- [Thunderfly TFI2CEXT01](https://docs.thunderfly.cz/avionics/TFI2CEXT01/)
- [Thunderfly TFI2CADT01](../sensor_bus/translator_tfi2cadt.md)