# CUAV P8 遥测无线电

CUAV P8 Radio 是一款远距离 (>60km) 高数据速率 (375 Kbps) 的无人机远程数据传输模块，可与 PX4 即插即用。

它支持点对点、点对多点及中继通信等多种模式。

![CUAV P8 Radio](../../assets/hardware/telemetry/cuav_p8_hero.png)

## 核心特性

- 远距离传输：>60km（根据天线和环境，最高可达 100 km）
- 支持点对点、点对多点、中继模式
- 最大功率 2W（固定频率 2W；跳频 1W）
- 最高传输速率 345 Kbps
- 支持 12v~60V 工作电压
- 可作为地面站调制解调器或机体调制解调器使用
- 独立电源供电实现更稳定运行
- USB Type-C 接口，集成 USB 转 UART 转换器

## 购买渠道

- [CUAV 官方店铺](https://www.cuav.net/en/p8-2/)
- [CUAV 阿里巴巴](https://www.alibaba.com/product_detail/Free-shipping-CUAV-UAV-P8-Radio_1600324379418.html?spm=a2747.manage.0.0.2dca71d2bY4B0M)

## PX4 配置

CUAV P8 Radio 已预配置（波特率 57600，广播模式）用于 PX4。
若连接至 `TELEM1` 或 `TELEM2` 接口则无需额外设置。

部分飞控或使用其他串口时，可能需要[配置串口实现 MAVLink 通信](../peripherals/mavlink_peripherals.md)。

:::tip
[P8 配置](https://doc.cuav.net/data-transmission/p8-radio/en/config.html) 提供了完整的无线电配置信息（如需）。
:::

## 引脚分配

![P8 引脚分配](../../assets/hardware/telemetry/cuav_p8_pinouts.png)

### 数据端口

| 引脚 | C-RTK GPS 6P | 引脚 | Pixhawk 标准引脚 |
| --- | ------------ | --- | ----------------- |
| 1   | 5V+(NC)      | 1   | VCC               |
| 2   | RX           | 2   | TX                |
| 3   | TX           | 3   | RX                |
| 4   | RTS          | 4   | RTS               |
| 5   | CTS          | 5   | CTS               |
| 6   | GND          | 6   | GND               |

## 接线

![P8 接线](../../assets/hardware/telemetry/cuav_p8_connect.png)

将 CUAV P8 Radio 连接到飞控的 `TELEM1`/`TELEM2` 接口，并通过电池或 BEC 为模块供电。
所需线缆已包含在包装中。

:::tip
CUAV P8 Radio 不支持从飞控供电，需要连接 12~60v 电池或 BEC。
:::

## 更多信息

[P8 手册](http://manual.cuav.net/data-transmission/p8-radio/p8-user-manual-en.pdf)

[CUAV P8 Radio](https://doc.cuav.net/data-transmission/p8-radio/en/) (官方指南)