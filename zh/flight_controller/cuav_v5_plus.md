# CUAV V5+ 自主飞控器

:::warning
PX4 不制造此（或任何其他）飞控器。
有关硬件支持或合规性问题，请联系[制造商](https://store.cuav.net/)。
:::

_V5+_<sup>&reg;</sup> 是由 CUAV<sup>&reg;</sup> 生产的先进自主飞控器。
该飞控器由 CUAV<sup>&reg;</sup> 与 PX4 团队合作设计。

该飞控器推荐用于商业系统集成，同时也适用于学术研究或其他任何用途。

![V5+ 自主飞控器 - 主视觉图](../../assets/flight_controller/cuav_v5_plus/v5+_01.png)

其主要功能包括：

- 与 [Pixhawk项目](https://pixhawk.org/) **FMUv5** 设计标准完全兼容，并采用 [Pixhawk连接器标准](https://pixhawk.org/pixhawk-connector-standard/) 处理所有外部接口
- 相比 FMU v3 具备更先进的处理器、更大的内存和闪存容量，并配备更稳定可靠的传感器
- 与 PX4 固件兼容
- 模块化设计允许用户自定义载板
- 内置高性能减震系统
- 多重冗余传感器和电源系统，提升飞行安全性和稳定性

::: info
该飞控器[获得制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 快速概览

- 主FMU处理器：STM32F765
  - 32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM
- IO处理器：STM32F100
  - 32位Arm® Cortex®-M3，24MHz，8KB SRAM
- 机载传感器：

  - 加速度计/陀螺仪：ICM-20689
  - 加速度计/陀螺仪：BMI055
  - 磁力计：IST8310
  - 气压计：MS5611

- 接口：
  - 8-14路PWM输出（6路来自IO，8路来自FMU）
  - FMU上3路专用PWM/捕获输入
  - 专用R/C输入用于CPPM
  - 专用R/C输入用于Spektrum / DSM和S.Bus，带模拟/PWM RSSI输入
  - 模拟/PWM RSSI输入
  - S.Bus舵机输出
  - 5个通用串行端口
  - 4个I2C端口
  - 4个SPI总线
  - 2个带串口电调的CAN总线
  - 2块电池电压/电流的模拟输入
- 电源系统：
  - 电源：4.3~5.4V
  - USB输入：4.75~5.25V
- 重量与尺寸：
  - 重量：90g
  - 尺寸：85.5\*42\*33mm
- 其他特性：

  - 工作温度：-20 ~ 80°c（实测值）
  
## 购买渠道

[CUAV Aliexpress](https://www.aliexpress.com/item/32890380056.html?spm=a2g0o.detail.1000060.1.7a7233e7mLTlVl&gps-id=pcDetailBottomMoreThisSeller&scm=1007.13339.90158.0&scm_id=1007.13339.90158.0&scm-url=1007.13339.90158.0&pvid=d899bfab-a7ca-46e1-adf2-72ad1d649822)（国际用户）

[CUAV Taobao](https://item.taobao.com/item.htm?spm=a1z10.5-c.w4002-21303114052.37.a28f697aeYzQx9&id=594262853015)（中国大陆用户）

::: 提示
飞控可能附带 Neo GPS 模块出售
:::

<a id="connection"></a>

## 连接（接线）

[CUAV V5+ Wiring Quickstart](../assembly/quick_start_cuav_v5_plus.md)

## 引脚分配

从[here](http://manual.cuav.net/V5-Plus.pdf)下载**V5+**的引脚分配。

## 电压规格

_V5+ AutoPilot_ 支持冗余电源 - 最多可使用三个电源：`Power1`、`Power2` 和 `USB`。您必须至少为这些电源之一供电，否则飞控将无法获得电力。

::: info
基于 FMUv5 的 FMUs（如 _V5+ AutoPilot_）配备 PX4IO 模块时，舵机电源总线仅由 FMU 监控。它既不为 FMU 供电，也不会向 FMU 提供电源。然而，标记为 **+** 的引脚是共用的，电池消除电路 (BEC) 可连接至任意舵机引脚组以为舵机电源总线供电。
:::

**正常运行最大额定值**

在以下条件下，系统将按此顺序使用所有电源供电：

1. `Power1` 和 `Power2` 输入（4.3V 至 5.4V）
1. `USB` 输入（4.75V 至 5.25V）

## 过流保护

_V5+_ 具备 5 伏外设和 5 伏高压的过流保护，可将电流限制在 2.5A。
_V5+_ 具备短路保护。

:::warning
最多可向标记为引脚 1 的连接器提供 2.5 A 电流（尽管这些连接器额定电流仅为 1 A）。
:::

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接到合适的硬件时，_QGroundControl_ 会自动安装预构建的固件。
:::

要[构建 PX4](../dev_setup/building_px4.md)以适配此目标平台：

```
make px4_fmu-v5_default
```

## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)通过**FMU调试**端口（`DSU7`）运行。  
该开发板没有I/O调试接口。

![调试端口（DSU7）](../../assets/flight_controller/cuav_v5_plus/debug_port_dsu7.jpg)

调试端口（`DSU7`）使用[JST BM06B](https://www.digikey.com.au/product-detail/en/jst-sales-america-inc/BM06B-GHS-TBT-LF-SN-N/455-1582-1-ND/807850)连接器，引脚分配如下：

| 引脚    | 信号             | 电压   |
| ------- | ---------------- | ------ |
| 1（红） | 5V+              | +5V    |
| 2（黑） | 调试TX（输出）   | +3.3V  |
| 3（黑） | 调试RX（输入）   | +3.3V  |
| 4（黑） | FMU_SWDIO        | +3.3V  |
| 5（黑） | FMU_SWCLK        | +3.3V  |
| 6（黑） | GND              | GND    |

产品套装包含一条便捷的调试电缆，可连接到`DSU7`端口。  
该电缆分为FTDI电缆（用于连接[PX4系统控制台](../debug/system_console.md)到计算机USB端口）和用于SWD/JTAG调试的SWD引脚。  
提供的调试电缆未连接到SWD端口`Vref`引脚（1）。

![CUAV调试电缆](../../assets/flight_controller/cuav_v5_plus/cuav_v5_debug_cable.jpg)

:::warning
SWD Vref引脚（1）使用5V作为参考电压，但CPU工作在3.3V！

某些JTAG适配器（SEGGER J-Link）会使用Vref电压来设置SWD线路的电压。  
对于直接连接到_Segger Jlink_的情况，我们建议使用标记为`DSM`/`SBUS`/`RSSI`的连接器第4引脚提供的3.3V，为JTAG提供`Vtref`（即提供3.3V而非5V）。

更多信息请参见[使用JTAG进行硬件调试](#使用JTAG进行硬件调试)。
:::

## 串口映射

| UART   | 设备     | 端口                                  |
| ------ | -------- | ------------------------------------- |
| UART1  | /dev/ttyS0 | GPS                                   |
| USART2 | /dev/ttyS1 | 遥测1（流控制）                       |
| USART3 | /dev/ttyS2 | 遥测2（流控制）                       |
| UART4  | /dev/ttyS3 | 遥测4                                 |
| USART6 | /dev/ttyS4 | TX 是来自 SBUS_RC 接口的遥控输入     |
| UART7  | /dev/ttyS5 | 调试控制台                            |
| UART8  | /dev/ttyS6 | PX4IO                                 |

<!-- Note: Got ports using https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

<a id="optional-hardware"></a>

## 外设

- [数字空速传感器](https://item.taobao.com/item.htm?spm=a1z10.3-c-s.w4002-16371268452.37.6d9f48afsFgGZI&id=9512463037)
- [遥测无线电模块](https://cuav.taobao.com/category-158480951.htm?spm=2013.1.w5002-16371268426.4.410b7a821qYbBq&search=y&catName=%CA%FD%B4%AB%B5%E7%CC%A8)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机型

任何可以使用普通RC舵机或Futaba S-Bus舵机控制的多旋翼、固定翼飞机、车体或船体。  
所有支持的配置可在[机型参考](../airframes/airframe_reference.md)中查看。

## 注意事项

#### 不要将数字或模拟电源模块插入配置为其他类型电源模块的接口

如果将模拟电源模块插入数字电源模块接口，会导致该总线上所有I2C设备停止工作。这将特别导致GPS的指南针因冲突而失效，并可能长期损坏FMU。

同样，数字电源模块插入模拟接口时将无法工作，并可能长期损坏/摧毁电源模块。

## 兼容性

CUAV采用了一些差异化设计，与部分硬件不兼容，如下所述。

<a id="compatibility_gps"></a>

#### GPS与其他设备不兼容

推荐用于_CUAV V5+_ 和 _CUAV V5 nano_ 的 _Neo v2.0 GPS_ 与其他Pixhawk飞控不完全兼容（具体表现为蜂鸣器部分不兼容，安全开关可能存在兼容性问题）。

UAVCAN [NEO V2 PRO GNSS接收器](http://doc.cuav.net/gps/neo-series-gnss/en/neo-v2-pro.html) 也可使用，且与其他飞控兼容。

<a id="compatibility_jtag"></a>

#### 使用JTAG进行硬件调试

`DSU7` FMU调试针脚1电压为5伏 - 不是CPU的3.3伏。

部分JTAG通过该电压设置通信时的IO电平。

直接连接_Segger Jlink_ 时，建议使用DSM/SBUS/RSSI针脚4的3.3伏作为调试接口Pin1的`Vtref`。

## 已知问题

以下问题与它们首次出现的批次号相关。
批次号是V01后面的四位生产日期，显示在飞控侧边的标签上。
例如，序列号Batch V011904（V01是V5的编号，1904是生产日期，即批次号）。

<a id="pin1_unfused"></a>

#### SBUS / DSM / RSSI 接口Pin1未加保护

:::warning
这是一个安全问题。
:::

请勿在SBUS / DSM / RSSI接口上连接其他设备（除遥控接收机外）- 这可能导致设备损坏。

- _发现于:_ Batches V01190904xxxx
- _修复于:_ Batches later than V01190904xxxx

## 进一步信息

- [CUAV V5+ 手册](http://manual.cuav.net/V5-Plus.pdf)
- [CUAV V5+ 文档](http://doc.cuav.net/flight-controller/v5-autopilot/en/v5+.html)
- [FMUv5 参考设计引脚图](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165)
- [CUAV GitHub](https://github.com/cuav)
- [底板设计参考](https://github.com/cuav/hardware/tree/master/V5_Autopilot/V5%2B/V5%2BBASE)
- [CUAV V5+ 接线快速入门](../assembly/quick_start_cuav_v5_plus.md)
- [基于 DJI FlameWheel450 使用 CUAV v5+ 的机体构建日志](../frames_multicopter/dji_f450_cuav_5plus.md)