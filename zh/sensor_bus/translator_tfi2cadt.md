# TFI2CADT01 - I²C地址转换器

[TFI2CADT01](https://docs.thunderfly.cz/avionics/TFI2CADT01/) 是一款与 Pixhawk 和 PX4 兼容的地址转换模块。

地址转换允许多个具有相同地址的 I2C 设备共存于同一 I2C 网络中。如果使用多个具有相同固定地址的设备，可能需要该模块。

该模块包含输入端和输出端，一侧连接主设备的传感器。输出端可连接需要地址转换的传感器。模块包含两组接插件，每组负责不同的地址转换。

![TFI2CADT - i2c地址转换器](../../assets/peripherals/i2c_tfi2cadt/tfi2cadt01a_both_sides.jpg)

::: info
[TFI2CADT01](https://github.com/ThunderFly-aerospace/TFI2CADT01) 采用 GPLv3 开源硬件设计。可通过 [ThunderFly](https://www.thunderfly.cz/) 公司或 [Tindie eshop](https://www.tindie.com/products/26353/) 购买。
:::

## 地址转换方法

TFI2CADT01 对调用地址执行异或（XOR）操作。因此，新的设备地址可通过原始地址与模块指定值执行异或操作获得。默认情况下，输出1使用 0x08 值进行异或，第二端口使用 0x78。通过短接焊跳线，可将第一端口异或值改为 0x0f，第二端口改为 0x7f。

如果需要自定义地址转换值，通过更改配置电阻即可设置任意异或值。

## 使用示例

转速计传感器 [TFRPM01](../sensor/thunderfly_tachometer.md) 可通过焊跳线设置两个不同地址。如果自动驾驶仪有三个总线，只能连接6个传感器且无总线剩余（2个可用地址 * 3个 I2C 端口）。在某些多旋翼或 VTOL 解决方案中，需要测量8个以上元件的转速。此情况下强烈推荐使用 [TFI2CADT01](https://www.tindie.com/products/26353/)。

![多个传感器](../../assets/peripherals/i2c_tfi2cadt/tfi2cadt01_multi_tfrpm01.jpg)

下图显示如何将6个 TFRPM01 连接到一个自动驾驶总线。通过添加另一个 TFI2CADT01，可再连接4个设备到同一总线。

[![多个传感器连接](https://mermaid.ink/img/pako:eNptkd9rwjAQx_-VcE8dtJB2ukEfBLEWfJCJy8CHvgRznQH7gzSBDfF_33VZB2oCyf3I576XcBc4dgohh08j-xMTRdUyWuX2I6LNErY7zJh0tuv1ubNP_7csSRZsudlHS22GHlGxAduhM3fEfrdNI1GS4emK8a85fwSyGyC9A0S5yVbrg_DZKfLtCxH9JsjhaU7VvI7pfK3_NCg_NXmO3pwl5uYt9D0yAXoWoFNP4yM9H-kspJ0FtF8CdObpURtiaNA0UisaymWsrsCesMEKcnIV1tKdbQVVeyXU9UpaXCttOwO5NQ5jGKf1_t0ep9gzhZY04sYnrz9BI4mU)](https://mermaid-js.github.io/mermaid-live-editor/edit#pako:eNptkd9rwjAQx_-VcE8dtJB2ukEfBLEWfJCJy8CHvgRznQH7gzSBDfF_33VZB2oCyf3I576XcBc4dgohh08j-xMTRdUyWuX2I6LNErY7zJh0tuv1ubNP_7csSRZsudlHS22GHlGxAduhM3fEfrdNI1GS4emK8a85fwSyGyC9A0S5yVbrg_DZKfLtCxH9JsjhaU7VvI7pfK3_NCg_NXmO3pwl5uYt9D0yAXoWoFNP4yM9H-kspJ0FtF8CdObpURtiaNA0UisaymWsrsCesMEKcnIV1tKdbQVVeyXU9UpaXCttOwO5NQ5jGKf1_t0ep9gzhZY04sYnrz9BI4mU)

::: info
TFI2CADT01 不包含 I²C 总线缓冲功能。该模块仅执行地址转换操作。
:::

## 技术规格

- 工作电压：3.3V - 5V
- 最大电流：50mA
- 工作温度：-40°C 至 +85°C
- 尺寸：20mm x 15mm
- 接口：I²C

## 安装指南

1. 将模块的 VCC 连接到 I²C 主设备的 VCC
2. 将模块的 GND 连接到 I²C 主设备的 GND
3. 将模块的 SDA 连接到 I²C 主设备的 SDA
4. 将模块的 SCL 连接到 I²C 主设备的 SCL
5. 通过跳线选择地址转换模式（默认模式无需跳线）

## 注意事项

- 避免在高温高湿环境中使用
- 焊接时注意防静电
- 不建议在高速 I²C 通信中使用
- 地址冲突时应先检查设备地址配置

## 常见问题

**Q:** 如何确定需要的地址转换值？  
**A:** 通过计算设备地址与目标地址的异或结果确定转换值。

**Q:** 能否连接多个 TFI2CADT01 模块？  
**A:** 可以，但需要确保每个模块使用不同的地址转换值。

**Q:** 地址转换会影响通信速度吗？  
**A:** 不会，地址转换在硬件层完成，不增加通信延迟。

## 相关资源

- [数据手册](https://www.thunderfly.cz/datasheet/TFI2CADT01.pdf)
- [开发套件](https://www.thunderfly.cz/kit/TFI2CADT01-kit)
- [技术支持](mailto:support@thunderfly.cz)

## 版权信息

© 2023 ThunderFly Technologies. 保留所有权利。  
本产品受 GPLv3 开源协议保护。  
商业使用需遵守 [开源协议条款](https://www.gnu.org/licenses/gpl-3.0.html)。