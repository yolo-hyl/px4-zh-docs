

# CUAV X7 飞控

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://www.cuav.net)
:::

[X7](http://doc.cuav.net/flight-controller/x7/en/x7.html)<sup>&reg;</sup> 飞控是一款高性能自动驾驶仪。它是工业无人机和大型重型无人机的理想选择，主要供应给商业制造商。

![CUAV x7](../../assets/flight_controller/cuav_x7/x7.jpg)

飞控采用模块化设计，可匹配不同底板。您可以为无人机设计专用载板，以提升商业系统的集成度、减少布线、提高系统可靠性并增强无人机竞争力（例如在载板中集成空速传感器、遥测设备甚至伴随计算机）。
CUAV 还提供了多种载板供您选择。

::: info
此飞控是[制造商支持的](../flight_controller/autopilot_manufacturer_supported.md)
:::

## 功能

- 内部减震
- 模块化设计，可作为DIY载板
- 支持USB_HS，下载日志更快（PX4尚未支持）
- 支持更多DShot输出
- 支持IMU加热，使传感器工作更佳
- 专用CAN电池端口
- 3组IMU传感器
- 车规级RM3100指南针
- 高性能处理器

:::tip
制造商 [CUAV 文档](https://doc.cuav.net/flight-controller/x7/en/) 是X7的权威参考。
应优先使用它们，因为其中包含最完整和最新的信息。
:::

## 快速摘要

- 主控FMU处理器：STM32H743
- 机载传感器：

  - 加速度计/陀螺仪：ICM-20689
  - 加速度计/陀螺仪：ICM-20649
  - 加速度计/陀螺仪：BMI088
  - 磁力计：RM3100
  - 气压计：MS5611\*2

- 接口：
  - 14路PWM输出（12路支持Dshot）
  - 支持多路遥控器输入(SBUs / CPPM / DSM)
  - 模拟/PWM RSSI输入
  - 2个GPS端口（GPS和UART4端口）
  - 4组I2C总线（2组专用I2C端口）
  - 2组CAN总线端口
  - 2组电源接口（Power A为通用ADC接口，Power C为DroneCAN电池接口）
  - 2组ADC输入
  - 1个USB接口
- 电源系统：
  - 供电：4.3~5.4V
  - USB输入：4.75~5.25V
  - 舵机电源输入：0~36V
- 重量与尺寸：
  - 重量：101g
- 其他特性：
  - 工作温度：-20 ~ 80°c（实测值）
  - 三组IMU
  - 支持温度补偿
  - 内置减震结构

::: info
当运行PX4固件时，仅8路PWM可用，剩余6路PWM仍在适配中，因此目前与VOLT不兼容。
:::

## 购买渠道

[CUAV 商店](https://store.cuav.net)

[CUAV 阿里速卖通](https://www.aliexpress.com/item/4001042683738.html?spm=a2g0o.detail.1000060.2.1ebb2a9d3WDryi&gps-id=pcDetailBottomMoreThisSeller&scm=1007.13339.169870.0&scm_id=1007.13339.169870.0&scm-url=1007.13339.169870.0&pvid=f0df2481-1c0a-44eb-92a4-9c11c6cb3d06&_t=gps-id:pcDetailBottomMoreThisSeller,scm-url:1007.13339.169870.0,pvid:f0df2481-1c0a-44eb-92a4-9c11c6cb3d06,tpp_buckets:668%230%23131923%2320_668%23808%234094%23518_668%23888%233325%2319_668%234328%2319934%23630_668%232846%238115%23807_668%232717%237566%23827_668%231000022185%231000066058%230_668%233468%2315607%2376)

## 连接（布线）

[CUAV X7 布线快速入门](http://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-x7.html)

## 尺寸和引脚分配

![CUAV x7](../../assets/flight_controller/cuav_x7/x7-size.jpg)

![X7 pinouts](../../assets/flight_controller/cuav_x7/x7-pinouts.jpg)

:::warning
`RCIN`端口仅限于为遥控接收机供电，不能连接到任何电源/负载。
:::

## 电压等级

当提供三个电源时，_X7 AutoPilot_ 的供电系统可实现三重冗余。  
供电轨包括：**POWERA**、**POWERC** 和 **USB**。

::: info  
输出供电轨 **PWM OUT**（0V 至 36V）不为飞控板供电（也不会被飞控板供电）。  
必须为 **POWERA**、**POWERC** 或 **USB** 中的至少一个供电轨供电，否则电路板将处于未通电状态。  
:::  

**正常运行最大电压规格**  

在此条件下，系统将按以下顺序使用所有可用电源供电：  

1. **POWERA** 和 **POWERC** 输入（4.3V 至 5.4V）  
2. **USB** 输入（4.75V 至 5.25V）

## 构建固件

:::tip
大多数用户不需要构建此固件！
当连接适当的硬件时，它会由_QGroundControl_预先构建并自动安装。
:::

要[构建PX4](../dev_setup/building_px4.md)以用于此目标：

```
make cuav_x7pro_default
```

## 过流保护

_X7_ 在5伏外设和5伏高功率接口上具有过流保护，将电流限制为2.5A。  
_X7_ 具备短路保护功能。  

:::警告  
最多可向标记为引脚1的接口提供2.5 A电流（尽管这些接口的额定电流仅为1 A）。  
:::

## 调试端口

系统的串口控制台和SWD接口在 **DSU7** 端口上运行。  
只需将FTDI线缆连接到DSU7接口（产品清单包含CUAV FTDI线缆）。

![调试端口 (DSU7)](../../assets/flight_controller/cuav_v5_plus/debug_port_dsu7.jpg)

[ PX4系统控制台](../debug/system_console.md) 和 [SWD接口](../debug/swd_debug.md) 在 **FMU调试** 端口（`DSU7`）上运行。

调试端口（`DSU7`）使用 [JST BM06B](https://www.digikey.com.au/product-detail/en/jst-sales-america-inc/BM06B-GHS-TBT-LF-SN-N/455-1582-1-ND/807850) 接口，其引脚分配如下：

| 引脚    | 信号            | 电压    |
| ------- | --------------- | ------- |
| 1 (红色) | 5V+            | +5V     |
| 2 (黑色) | 调试TX（输出） | +3.3V   |
| 3 (黑色) | 调试RX（输入） | +3.3V   |
| 4 (黑色) | FMU_SWDIO      | +3.3V   |
| 5 (黑色) | FMU_SWCLK      | +3.3V   |
| 6 (黑色) | GND            | GND     |

CUAV提供了专用的调试线缆，可连接到 `DSU7` 端口。  
该线缆分接出FTDI线缆，用于将 [PX4系统控制台](../debug/system_console.md) 连接到计算机USB端口，以及用于SWD/JTAG调试的SWD引脚。  
提供的调试线缆未连接到SWD端口 `Vref` 引脚（1）。

![CUAV调试线缆](../../assets/flight_controller/cuav_v5_plus/cuav_v5_debug_cable.jpg)

:::warning
SWD Vref引脚（1）使用5V作为Vref，但CPU运行电压为3.3V！

部分JTAG适配器（SEGGER J-Link）会使用Vref电压来设置SWD线路的电压。  
对于直接连接到 _Segger Jlink_ 的情况，我们建议您使用标记为 `DSM`/`SBUS`/`RSSI` 的接口第4引脚的3.3伏电压，为JTAG提供 `Vtref`（即提供3.3V而非5V）。
:::

## 支持的平台 / 机型

任何可以使用普通RC舵机或Futaba S-Bus舵机进行控制的多旋翼/固定翼/地面车或船只。所有支持的配置可在[机型参考](../airframes/airframe_reference.md)中查看。

## 进一步信息

- [快速入门](http://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-x7.html)
- [CUAV 文档](http://doc.cuav.net)
- [x7 电路图](https://github.com/cuav/hardware/tree/master/X7_Autopilot)