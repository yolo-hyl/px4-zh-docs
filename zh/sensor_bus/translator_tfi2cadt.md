

# TFI2CADT01 - I²C 地址转换器

[TFI2CADT01](https://docs.thunderfly.cz/avionics/TFI2CADT01/) 是一个与 Pixhawk 和 PX4 兼容的地址转换模块。

地址转换允许多个具有相同地址的 I2C 设备在 I2C 网络中共存。
如果使用多个具有相同硬编码地址的设备，可能需要该模块。

该模块具有输入侧和输出侧，传感器连接在一侧的主设备上。
在输出侧，需要转换地址的传感器可以连接。
模块包含两组接插件，每组负责不同的地址转换。

![TFI2CADT - I²C地址转换器](../../assets/peripherals/i2c_tfi2cadt/tfi2cadt01a_both_sides.jpg)

::: info
[TFI2CADT01](https://github.com/ThunderFly-aerospace/TFI2CADT01) 采用开源硬件设计，许可证为 GPLv3。
该产品可通过 [ThunderFly](https://www.thunderfly.cz/) 公司或 [Tindie eshop](https://www.tindie.com/products/26353/) 商业购买。
:::

## 地址转换方法

TFI2CADT01 在调用地址时执行异或（XOR）操作。
因此，可以通过将原始地址与模块上指定的值进行异或操作来获取新设备地址。
默认情况下，输出 1 与 0x08 值执行异或操作，第二个端口与 0x78 值执行异或操作。
通过短接焊桥，可将第一个端口的异或值更改为 0x0f，第二个端口更改为 0x7f。

如果需要自定义地址转换值，通过更改配置电阻即可设置任意异或值。

## 使用示例

转速计传感器 [TFRPM01](../sensor/thunderfly_tachometer.md) 可通过焊桥设置为两个不同的地址。
如果飞控器有三个总线接口，最多只能连接 6 个传感器，且没有空闲总线（2 个可用地址 * 3 个 I2C 接口）。
在某些多旋翼或垂直起降（VTOL）解决方案中，需要测量 8 个或更多部件的转速。
在这种情况下，强烈推荐使用 [TFI2CADT01](https://www.tindie.com/products/26353/)。

![多个传感器](../../assets/peripherals/i2c_tfi2cadt/tfi2cadt01_multi_tfrpm01.jpg)

下图展示了如何将 6 个 TFRPM01 连接到一个飞控总线。
通过添加另一个 TFI2CADT01，可以在同一总线上连接 4 个额外设备。

[![多个传感器连接](https://mermaid.ink/img/pako:eNptkd9rwjAQx_-VcE8dtJB2ukEfBLEWfJCJy8CHvgRznQH7gzSBDfF_33VZB2oCyf3I576XcBc4dgohh08j-xMTRdUyWuX2I6LNErY7zJh0tuv1ubNP_7csSRZsudlHS22GHlGxAduhM3fEfrdNI1GS4emK8a85fwSyGyC9A0S5yVbrg_DZKfLtCxH9JsjhaU7VvI7pfK3_NCg_NXmO3pwl5uYt9D0yAXoWoFNP4yM9H-kspJ0FtF8CdObpURtiaNA0UisaymWsrsCesMEKcnIV1tKdbQVVeyXU9UpaXCttOwO5NQ5jGKf1_t0ep9gzhZY04sYnrz9BI4mU)](https://mermaid-js.github.io/mermaid-live-editor/edit#pako:eNptkd9rwjAQx_-VcE8dtJB2ukEfBLEWfJCJy8CHvgRznQH7gzSBDfF_33VZB2oCyf3I576XcBc4dgohh08j-xMTRdUyWuX2I6LNErY7zJh0tuv1ubNP_7csSRZsudlHS22GHlGxAduhM3fEfrdNI1GS4emK8a85fwSyGyC9A0S5yVbrg_DZKfLtCxH9JsjhaU7VvI7pfK3_NCg_NXmO3pwl5uYt9D0yAXoWoFNP4yM9H-kspJ0FtF8CdObpURtiaNA0UisaymWsrsCesMEKcnIV1tKdbQVVeyXU9UpaXCttOwO5NQ5jGKf1_t0ep9gzhZY04sYnrz9BI4mU)


<!-- original mermaid graph
graph TD
    FMU(FMU - PX4 autopilot)
    FMU -- > AIR(Airspeed sensor)
    FMU -- > RPM1(TFRPM01C 0x50)
    FMU -- > RPM2(TFRPM01C 0x51)
    FMU -- > TFI2CEXT
    TFI2CEXT -- > ADT(TFI2CADT01: 0x0f, 0x7f)
    ADT -- > RPM3(TFRPM01C 0x52)
    ADT -- > RPM4(TFRPM01C 0x53)
    ADT -- > RPM5(TFRPM01C 0x54)
    ADT -- > RPM6(TFRPM01C 0x55)
    ADT -- > RPM7(TFRPM01C 0x56)
    ADT -- > RPM8(TFRPM01C 0x57)
-->

::: info
TFI2CADT01 不包含任何 I2C 缓冲器或加速器。
由于它会在总线上增加额外的电容，我们建议将其与某些总线增强器结合使用，例如：[TFI2CEXT01](https://docs.thunderfly.cz/avionics/TFI2CEXT01/).
:::

### 其他资源

* Datasheet of [LTC4317](https://www.analog.com/media/en/technical-documentation/data-sheets/4317fa.pdf)