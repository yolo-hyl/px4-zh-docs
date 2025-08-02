

# NXP RDDRONE-FMUK66 FMU (已停产)

<Badge type="info" text="已停产" />

:::warning
该飞控已[停产](../flight_controller/autopilot_experimental.md)且不再商业销售。
:::

:::warning
PX4 不生产该(或任何)自动驾驶仪。
请联系[NXP](https://www.nxp.com/)获取硬件支持或合规性问题咨询。
:::

RDDRONE-FMUK66 FMU 是基于 NXP 半导体组件的参考设计，其紧密遵循 Pixhawk FMUv4 规格，并增加了双线汽车以太网 100BASE-T1 和安全元件 A71CH (RevC) 或 SE050 (RevD)。NXP 提供原理图、gerbers、BOM 和源文件，以便任何人可以复制、修改或重新设计此方案。

这是官方推荐与 [HoverGames](https://www.hovergames.com/) 配套使用的FMU。

![RDDRONE-FMUK66 FMU 主视觉图1](../../assets/flight_controller/nxp_rddrone_fmuk66/HoverGamesDrone_14042019_XL_020.jpg)

![RDDRONE-FMUK66 FMU 主视觉图2](../../assets/flight_controller/nxp_rddrone_fmuk66/HoverGamesDrone_14042019_XL_021.jpg)

NXP FMU 及配套外设已通过 FCC/CE/RoHS/REACH 指令测试认证。

::: info
这些飞控支持[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 快速摘要

- **主FMU处理器：**  
  - Kinetis K66 MK66FN2MOVLQ18 微控制器，运行于180MHz Cortex-M4F MCU，2MB Flash，256KB SRAM，双USB（全速+高速），以太网，144-LQFP封装  
- **板载传感器：**  
  - 加速度计/陀螺仪：BMI088/ICM42688（RevD）  
  - 加速度计/磁力计：FXOS8700CQ  
  - 陀螺仪：FXAS21002CQ  
  - 磁力计：BMM150  
  - 气压计：ML3115A2  
  - 气压计：BMP280  
- **GPS：**  
  - u-blox Neo-M8N GPS/GLONASS接收器，集成磁力计IST8310  

此FMU仅以套件形式提供，包含 [Segger Jlink EDU mini 调试器](https://www.segger.com/products/debug-probes/j-link/models/j-link-edu-mini/)、DCD-LZ调试适配器、USB-TTL-3V3控制台线缆、HolyBro GPS模块、电池电源模块、SD卡和外壳、螺丝和贴纸。  
遥测无线电（[HGD-TELEM433](https://www.nxp.com/part/HGD-TELEM433) 和 [HGD-TELEM915](https://www.nxp.com/part/HGD-TELEM915)）需单独购买以匹配您所在国家使用的ISM频段频率。  

![RDDRONE-FMUK66 FMU套件](../../assets/flight_controller/nxp_rddrone_fmuk66/rddrone_fmu66_kit_img_contents.jpg)  

还提供不含电源模块、GPS、Jlink或USB-TTL-3V3控制台线缆或SD卡的“Lite”版本 RDDRONE-FMUK66L。[向下滚动查看FMUK66购买页面中的FMUK66L](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66#buy)  

更多信息请参阅 [技术数据表](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66) <!-- www.nxp.com/rddrone-fmuk66 -->

## 购买地点

**RDDRONE-FMUK66** 参考设计套件可直接从 NXP 或其全球授权网络的 [电子分销商](https://www.nxp.com/support/sample-and-buy/distributor-network:DISTRIBUTORS) 购买。

- [购买链接](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66#buy) (www.nxp.com)
- 遥测无线电根据频段单独购买：
  - [HGD-TELEM433](https://www.nxp.com/part/HGD-TELEM433)
  - [HGD-TELEM915](https://www.nxp.com/part/HGD-TELEM915)

::: info
_RDDRONE-FMUK66_ FMU 也包含在完整的 HoverGames 无人机套件中：[KIT-HGDRONEK66](https://www.nxp.com/applications/solutions/industrial/aerospace-and-mobile-robotics/uavs-drones-and-rovers/nxp-hovergames-drone-kit-including-rddrone-fmuk66-and-peripherals:KIT-HGDRONEK66#buy)
:::

## 连接器

[连接器图示]

## 引脚分配

[引脚分配列表或链接]

## 尺寸

[尺寸]

-->

## 组装/设置

https://nxp.gitbook.io/hovergames

## 构建固件

:::tip
大多数用户无需构建此固件！
当连接到合适的硬件时，_QGroundControl_ 会自动预构建并安装固件。
:::

要[构建PX4](../dev_setup/building_px4.md)以用于此目标：

```
make nxp_fmuk66-v3_default
```

## 调试端口

[PX4系统控制台](../debug/system_console.md)和[SWD接口](../debug/swd_debug.md)运行在[DCD-LZ FMU调试](https://nxp.gitbook.io/hovergames/rddrone-fmuk66/connectors/debug-interface-dcd-lz)端口上。

NXP的DCD-LZ是一个7针JST-GH连接器，它在[Pixhawk 6针标准调试端口](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf)基础上增加了nRST/MCU_RESET引脚。

DCD-LZ扩展适配器允许使用标准10针JTAG/SWD接口（即使用Segger Jlink）和标准5针FTDI USB-TTL-3V3类型电缆。



<!--

## Peripherals

* [与该硬件配合使用的任何设备列表]
-->



## 支持的平台/机体构型

任何可通过普通RC舵机或Futaba S-Bus舵机控制的多旋翼/固定翼/地面车或船只。  
所有支持的配置可在[Airframes Reference](../airframes/airframe_reference.md)中查看。

![HoverGames Drone Kit](../../assets/flight_controller/nxp_rddrone_fmuk66/hovergames_drone_14042019_xl001.jpg)

:::tip  
NXP [HoverGames Drone Kit](https://www.nxp.com/kit-hgdronek66)（如上图所示）是一个完整的无人机开发套件，包含构建四旋翼飞行器所需的所有组件。  
您只需提供3S/4S锂电池。  
:::

## 进一步信息

- [HoverGames在线文档](https://nxp.gitbook.io/hovergames) PX4用户和编程指南，包含特制装配、构建、调试和编程说明。

- 支持HoverGames和RDDRONE-FMUK66的3D模型可在_Thingiverse_通过以下搜索链接找到：[fmuk66](https://www.thingiverse.com/search?q=fmuk66&type=things&sort=relevant)，[hovergames](https://www.thingiverse.com/search?q=hovergames&type=things&sort=relevant)。

![HoverGamesDronelogo](../../assets/flight_controller/nxp_rddrone_fmuk66/hovergames_colored_small.png)