# CUAV V5 nano自动驾驶仪

:::warning
PX4不生产此（或任何）自动驾驶仪。  
联系[制造商](https://store.cuav.net/)获取硬件支持或合规性问题。
:::

**V5 nano**<sup>&reg;</sup> 是CUAV<sup>&reg;</sup>与PX4团队合作设计的用于空间受限应用的自动驾驶仪。

该自动驾驶仪足够小巧，可用于220mm竞速无人机，同时仍具备适用于大多数无人机应用的强大性能。

![V5 nano - Hero image](../../assets/flight_controller/cuav_v5_nano/v5_nano_01.png)

::: info
V5 nano与[CUAV V5+](../flight_controller/cuav_v5_plus.md)类似，但采用一体化结构，PWM端口较少（无法用于需要[AUX端口](../airframes/airframe_reference.md)的机型），且无内部减震。
:::

其主要特性包括：

- 与[Pixhawk项目](https://pixhawk.org/)的**FMUv5**设计标准完全兼容，所有外部接口均采用[Pixhawk连接器标准](https://pixhawk.org/pixhawk-connector-standard/)。
- 相比FMU v3具备更先进的处理器、RAM和闪存，以及更稳定可靠的传感器。
- 固件兼容PX4。
- I/O针脚间距达到2.6mm，便于使用所有接口。

::: info
此飞控[获得制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

### 快速概览

主FMU处理器：STM32F765◦32位Arm® Cortex®-M7，216MHz，2MB内存，512KB RAM

- 板载传感器：

  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：ICM-20602  
  - 加速度计/陀螺仪：BMI055  
  - 磁力计：IST8310  
  - 气压计：MS5611

- 接口：8个PWM输出

  - 3个专用PWM/Capture输入（FMU）  
  - 专用R/C输入（CPPM）  
  - 专用R/C输入（Spektrum/DSM和S.Bus）  
  - 模拟/PWM RSSI输入  
  - 4个通用串口  
  - 3个I2C端口  
  - 4个SPI总线  
  - 2个CAN总线  
  - 电池电压/电流模拟输入  
  - 2个额外模拟输入  
  - 支持nARMED

- 电源系统：电源砖输入：4.75~5.5V  
  USB电源输入：4.75~5.25V

- 重量和尺寸：
  - 尺寸：60\*40\*14mm
- 其他特性：
  - 工作温度：-20 ~ 85°C（实测值）## 哪里购买

[CUAV 商店](https://store.cuav.net/shop/v5-nano/)

[CUAV 阿里速卖通](https://www.aliexpress.com/item/33050770314.html?storeId=3257035&spm=2114.12010612.8148356.9.dbe6790bjW2hpH)（国际用户）

[CUAV 淘宝](https://item.taobao.com/item.htm?spm=a230r.1.14.8.26ab5258veQJRu&id=569404317857&ns=1&abbucket=13#detail)（中国大陆用户）

::: info
自动驾驶仪可能包含Neo GPS模块
:::

<a id="connection"></a>## 连接（布线）

[V5 nano 布线快速入门](../assembly/quick_start_cuav_v5_nano.md)

## 引脚分配

从 [here](http://manual.cuav.net/V5-Plus.pdf) 下载 **V5 nano** 引脚分配文件。

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接合适硬件时，_QGroundControl_ 会自动安装预编译版本。
:::

要[构建 PX4](../dev_setup/building_px4.md) 以支持本目标平台：

```
make px4_fmu-v5_default
```

<a id="debug_port"></a>## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)通过**FMU调试**端口（`DSU7`）运行。  
该板没有I/O调试接口。

![调试端口（DSU7）](../../assets/flight_controller/cuav_v5_nano/debug_port_dsu7.jpg)

调试端口（`DSU7`）使用[JST BM06B](https://www.digikey.com.au/product-detail/en/jst-sales-america-inc/BM06B-GHS-TBT-LF-SN-N/455-1582-1-ND/807850)连接器，其引脚分配如下：

| 引脚    | 信号         | 电压  |
| ------- | ------------ | ----- |
| 1（红色） | 5V+          | +5V   |
| 2（黑色） | DEBUG TX（输出） | +3.3V |
| 3（黑色） | DEBUG RX（输入） | +3.3V |
| 4（黑色） | FMU_SWDIO    | +3.3V |
| 5（黑色） | FMU_SWCLK    | +3.3V |
| 6（黑色） | GND          | GND   |

产品包装包含一条便捷的调试线缆，可连接到`DSU7`端口。  
该线缆包含一条FTDI线缆用于将[PX4系统控制台](../debug/system_console.md)连接到计算机USB端口，以及用于SWD/JTAG调试的SWD引脚。  
提供的调试线缆未连接到SWD端口的`Vref`引脚（1）。

![CUAV调试线缆](../../assets/flight_controller/cuav_v5_nano/cuav_nano_debug_cable.jpg)

:::warning
SWD Vref引脚（1）使用5V作为参考电压，但CPU运行在3.3V！

某些JTAG适配器（如SEGGER J-Link）会使用Vref电压来设置SWD线路的电压。  
对于直接连接到_Segger Jlink_，我们建议使用标记为`DSM`/`SBUS`/`RSSI`的连接器第4引脚提供的3.3V电源作为JTAG的`Vtref`（即提供3.3V而非5V）。

更多详情请参见[Using JTAG for hardware debugging](#using-jtag-for-hardware-debugging)。
:::

## 串口映射

| UART   | 设备         | 端口                                  |
| ------ | ------------ | ------------------------------------- |
| UART1  | /dev/ttyS0   | GPS                                   |
| USART2 | /dev/ttyS1   | TELEM1 (流控制)                       |
| USART3 | /dev/ttyS2   | TELEM2 (流控制)                       |
| UART4  | /dev/ttyS3   | TELEM4                                |
| USART6 | /dev/ttyS4   | TX 是来自 SBUS_RC 接口的遥控输入      |
| UART7  | /dev/ttyS5   | 调试控制台                            |
| UART8  | /dev/ttyS6   | 未连接 (无 PX4IO)                     |

<!-- 注：端口信息获取自 https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->## 电压规格

_V5 nano_ 必须通过 `Power` 连接器在飞行时供电，也可通过 `USB` 连接器进行台架测试供电。

::: info
`PM2` 连接器不能用于为 _V5 nano_ 供电（参见[此问题](#compatibility_pm2)）。
:::

::: info
Servo Power Rail 既不会由 FMU 供电，也不会向 FMU 提供电力。
不过，标记为 **+** 的引脚是共用的，可以通过连接任意舵机引脚组的 BEC 为舵机电源轨供电。
:::

## 过流保护

_V5 nano_ 没有过流保护。

<a id="Optional-hardware"></a>## 外设

- [数字空速传感器](https://item.taobao.com/item.htm?spm=a1z10.3-c-s.w4002-16371268452.37.6d9f48afsFgGZI&id=9512463037)
- [遥测无线电模块](https://cuav.taobao.com/category-158480951.htm?spm=2013.1.w5002-16371268426.4.410b7a821qYbBq&search=y&catName=%CA%FD%B4%AB%B5%E7%CC%A8)
- [测距仪/距离传感器](../sensor/rangefinders.md)

## 支持的平台 / 机体结构

任何可以使用普通遥控舵机或Futaba S-Bus舵机控制的多旋翼/固定翼飞机/地面车或船只。所有受支持的配置均可在[Airframes Reference](../airframes/airframe_reference.md)中查看。

## 兼容性

CUAV 采用了一些差异化设计，与部分硬件不兼容，下面将进行说明。

<a id="compatibility_gps"></a>

#### Neo v2.0 GPS 与其他设备不兼容

推荐与 _CUAV V5+_ 和 _CUAV V5 nano_ 一起使用的 _Neo v2.0 GPS_ 与其他 Pixhawk 飞控不完全兼容（具体而言，蜂鸣器部分不兼容，安全开关可能存在兼容性问题）。

UAVCAN [NEO V2 PRO GNSS 接收器](http://doc.cuav.net/gps/neo-series-gnss/en/neo-v2-pro.html) 也可以使用，且与其他飞控兼容。

<a id="compatibility_jtag"></a>

#### 使用 JTAG 进行硬件调试

`DSU7` FMU 调试引脚 1 是 5 伏电压 - 而不是 CPU 的 3.3 伏电压。

部分 JTAG 探针使用该电压在与目标设备通信时设置 IO 电平。

直接连接到 _Segger Jlink_ 时，我们建议您使用 DSM/SBUS/RSSI 引脚 4 的 3.3 伏作为调试连接器上的引脚 1 (`Vtref`)。

<a id="compatibility_pm2"></a>

#### PM2 无法为飞控供电

`PM2` 仅能测量电池电压和电流，但 **不能** 为飞控供电。

:::warning
PX4 不支持此接口。
:::

## 已知问题

以下问题与它们首次出现的 _批次号_ 相关。  
批次号是V01后面的四位生产日期，显示在飞控侧面的贴纸上。  
例如，序列号 Batch V011904（(V01是V5的编号，1904是生产日期，即批次号)。

<a id="pin1_unfused"></a>

#### SBUS / DSM / RSSI 接口 Pin1 未保险丝

:::warning
This is a safety issue.
:::

请勿在 SBUS / DSM / RSSI 接口上连接其他设备（除遥控接收机外）——这可能导致设备损坏！

- _发现于:_ Batches V01190904xxxx  
- _修复于:_ Batches later than V01190904xxxx## 进一步信息

- [V5 nano 手册](http://manual.cuav.net/V5-nano.pdf) (CUAV)
- [FMUv5 参考设计引脚分配](https://docs.google.com/spreadsheets/d/1-n0__BYDedQrc_2NHqBenG1DNepAgnHpSGglke-QQwY/edit#gid=912976165) (CUAV)
- [CUAV Github](https://github.com/cuav) (CUAV)
- [使用 CUAV v5 nano 构建 DJI FlameWheel450 机架的构建日志](../frames_multicopter/dji_f450_cuav_5nano.md)