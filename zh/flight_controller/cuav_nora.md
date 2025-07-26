

# CUAV 诺拉飞行控制器

:::warning
PX4不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://www.cuav.net)。
:::

[诺拉](https://doc.cuav.net/flight-controller/x7/en/nora.html)<sup>&reg;</sup> 飞行控制器是一款高性能自动驾驶仪。
它是工业无人机和大型重型无人机的理想选择。
它主要供应给商业制造商。

![CUAV x7](../../assets/flight_controller/cuav_nora/nora.png)

诺拉是CUAV X7的一种变体。
它采用集成主板（软硬板一体），减少了飞行控制器的内部连接器，提高了可靠性，并将所有接口置于侧面（使布线更加简洁）。

::: info
此飞行控制器是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 功能特性

- 内部减震设计
- 集成工艺减少了因接口损坏导致的故障
- 支持 USB_HS，可更快下载日志（PX4 尚未支持）
- 支持更多 dshot 输出
- 支持 IMU 加热，使传感器工作更佳
- 专用 CAN 电池接口
- 3 组 IMU 传感器
- 车规级 RM3100 罗盘
- 高性能处理器

:::提示
制造商 [CUAV Docs](https://doc.cuav.net/flight-controller/x7/en/nora.html) 是 Nora 的权威参考文档
它们应作为首选参考资料，因为包含最完整和最新的信息
:::

## 快速概述

- 主FMU处理器：STM32H743  
- 机载传感器：  

  - 加速度计/陀螺仪：ICM-20689  
  - 加速度计/陀螺仪：ICM-20649  
  - 加速度计/陀螺仪：BMI088  
  - 磁力计：RM3100  
  - 气压计：MS5611\*2  

- 接口：  
  - 14路PWM输出（12路支持Dshot）  
  - 支持多种RC输入（SBUs / CPPM / DSM）  
  - 模拟/PWM RSSI输入  
  - 2个GPS端口（GPS和UART4端口）  
  - 4路i2c总线（两个i2c专用端口）  
  - 2个CAN总线端口  
  - 2个电源端口（Power A是通用ADC接口，Power C是DroneCAN电池接口）  
  - 2路ADC输入  
  - 1个USB端口  
- 电源系统：  
  - 供电电压：4.3~5.4V  
  - USB输入电压：4.75~5.25V  
  - 伺服供电输入：0~36V  
- 重量与尺寸：  
  - 重量：101克  
- 其他特性：  
  - 工作温度：-20 ~ 80°C（实测值）  
  - 三个IMU  
  - 支持温度补偿  
  - 内部减震  

::: info  
当运行PX4固件时，仅有8路PWM输出可用。  
剩余的6路PWM端口仍在适配中（因此在撰写时与VOLT不兼容）。  
:::

## 购买渠道

- [CUAV 商店](https://store.cuav.net)<\br>
- [CUAV 阿里速卖通](https://www.aliexpress.com/item/4001042501927.html?gps-id=8041884&scm=1007.14677.110221.0&scm_id=1007.14677.110221.0&scm-url=1007.14677.110221.0&pvid=3dc0a3ba-fa82-43d2-b0b3-6280e4329cef&spm=a2g0o.store_home.promoteRecommendProducts_7913969.58)

## 连接（接线）

[CUAV nora 接线快速入门](https://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-nora.html)

## 尺寸和引脚分配

![CUAV x7](../../assets/flight_controller/cuav_nora/nora-size.jpg)

![X7 pinouts](../../assets/flight_controller/cuav_nora/nora-pinouts.jpg)

:::warning
`RCIN`端口仅限于为rc接收器供电，不能连接到任何电源/负载。
:::

## 电压规格

Nora AutoPilot\* 的电源系统可实现三重冗余，当提供三个电源时即可实现。两个电源轨分别为：**POWERA**、**POWERC** 和 **USB**。

::: info
输出电源轨 **PWM OUT**（0V 至 36V）不会为飞控板供电（也不会由飞控板供电）。
你必须为 **POWERA**、**POWERC** 或 **USB** 中的任意一个供电，否则电路板将无电。
:::

**正常操作最大规格**

在以下条件下，系统将按此顺序使用所有电源供电：

1. **POWERA** 和 **POWERC** 输入（4.3V 至 5.4V）
2. **USB** 输入（4.75V 至 5.25V）

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接适当硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要为该目标[构建 PX4](../dev_setup/building_px4.md)：

```
make cuav_nora_default
```

## 过流保护

_Nora_ 在5伏外设和5伏高功率接口上具有过流保护，将电流限制在2.5A。  
_Nora_ 具有短路保护。  

:::warning  
最多可向标记为pin 1的连接器提供2.5A电流（尽管这些连接器额定电流仅为1A）。  
:::

## 调试端口

系统的串行控制台和SWD接口在**DSU7**端口上运行。  
只需将FTDI线缆连接到DSU7连接器（产品列表包含CUAV FTDI线缆）。  

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)在**FMU调试**端口（`DSU7`）上运行。  

调试端口（`DSU7`）使用[JST BM06B](https://www.digikey.com.au/product-detail/en/jst-sales-america-inc/BM06B-GHS-TBT-LF-SN-N/455-1582-1-ND/807850)连接器，其引脚分配如下：  

| 引脚    | 信号           | 电压   |  
| ------- | -------------- | ----- |  
| 1 (红)  | 5V+            | +5V   |  
| 2 (黑)  | 调试 TX (OUT)  | +3.3V |  
| 3 (黑)  | 调试 RX (IN)   | +3.3V |  
| 4 (黑)  | FMU_SWDIO      | +3.3V |  
| 5 (黑)  | FMU_SWCLK      | +3.3V |  
| 6 (黑)  | GND            | GND   |  

CUAV提供专用的调试线缆，可连接到`DSU7`端口。该线缆分出一条FTDI线缆，用于将[PX4系统控制台](../debug/system_console.md)连接到计算机的USB端口，并分出SWD引脚用于SWD/JTAG调试。提供的调试线缆未连接到SWD端口的`Vref`引脚（1）。  

![CUAV调试线缆](../../assets/flight_controller/cuav_v5_plus/cuav_v5_debug_cable.jpg)  

:::warning  
SWD Vref引脚（1）使用5V作为参考电压，但CPU运行在3.3V！  

某些JTAG适配器（如SEGGER J-Link）会使用Vref电压来设置SWD线路的电压。  
对于直接连接到_Segger Jlink_，我们建议使用标记为`DSM`/`SBUS`/`RSSI`的连接器第4引脚的3.3V，为JTAG提供`Vtref`（即提供3.3V，而非5V）。  
:::

## 支持的平台/机架

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/车或船。
所有支持的配置可参考 [机架参考](../airframes/airframe_reference.md)。

## 更多信息

- [快速入门](https://doc.cuav.net/flight-controller/x7/en/quick-start/quick-start-nora.html)
- [CUAV docs](http://doc.cuav.net)
- [nora schematic](https://github.com/cuav/hardware/tree/master/X7_Autopilot)