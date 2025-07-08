# NXP RDDRONE-FMUK66 FMU（已停产）

<Badge type="info" text="已停产" />

:::warning
此飞控已[停产](../flight_controller/autopilot_experimental.md)，不再商业供应。
:::

:::warning
PX4 不生产此（或任何）自动驾驶仪。
有关硬件支持或合规性问题，请联系[制造商](https://www.nxp.com/)。
:::

RDDRONE-FMUK66 FMU 是基于 NXP 半导体组件的参考设计，其紧密遵循 Pixhawk FMUv4 规格，同时增加了双线汽车以太网 100BASE-T1 和安全元件 A71CH（RevC）或 SE050（RevD）。
NXP 提供原理图、Gerber 文件、BOM 和源文件，以便任何人复制、修改或重新使用此设计。

这是与 [HoverGames](https://www.hovergames.com/) 官方搭配使用的 FMU。

![RDDRONE-FMUK66 FMU 主视觉图1](../../assets/flight_controller/nxp_rddrone_fmuk66/HoverGamesDrone_14042019_XL_020.jpg)

![RDDRONE-FMUK66 FMU 主视觉图2](../../assets/flight_controller/nxp_rddrone_fmuk66/HoverGamesDrone_14042019_XL_021.jpg)

NXP FMU 及其配套外设已通过 FCC/CE/RoHs/REACH 指令的测试认证。

::: info
这些飞控获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 快速摘要

- **主 FMU 处理器：**
  - Kinetis K66 MK66FN2MOVLQ18 微控制器，运行于 180MHz Cortex-M4F MCU，2MB 闪存，256KB SRAM，双 USB（FS + HS），以太网，144-LQFP。
- **板载传感器：**
  - 加速度计/陀螺仪：BMI088/ICM42688（RevD）...
  - 加速度计/磁强计：FXOS8700CQ
  - 陀螺仪：FXAS21002CQ
  - 磁强计：BMM150
  - 气压计：ML3115A2
  - 气压计：BMP280
- **GPS：**
  - u-blox Neo-M8N GPS/GLONASS 接收器；集成磁强计 IST8310

该 FMU 仅以套件形式提供，包含 [Segger Jlink EDU mini 调试器](https://www.segger.com/products/debug-probes/j-link/models/j-link-edu-mini/)、DCD-LZ 调试器适配器、USB-TTL-3V3 控制台电缆、HolyBro GPS 模块、电池电源模块、SD 卡和外壳、螺丝和贴纸。
遥测无线电（[HGD-TELEM433](https://www.nxp.com/part/HGD-TELEM433) 和 [HGD-TELEM915](https://www.nxp.com/part/HGD-TELEM915)）需单独购买以匹配您所在国家使用的 ISM 频段。

![RDDRONE-FMUK66 FMU 套件](../../assets/flight_controller/nxp_rddrone_fmuk66/rddrone_fmu66_kit_img_contents.jpg)

还有一个不包含电源模块、GPS、Jlink 或 USB-TTL-3V3 控制台电缆或 SD 卡的“Lite”版本 RDDRONE-FMUK66L。[向下滚动查看购买页面中 FMUK66 的 FMUK66L](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66#buy)

更多信息请参阅 [技术数据表](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66)。 <!-- www.nxp.com/rddrone-fmuk66 -->

## 购买渠道

**RDDRONE-FMUK66** 参考设计套件可直接从 NXP 或 NXP 授权的全球 [电子分销商网络](https://www.nxp.com/support/sample-and-buy/distributor-network:DISTRIBUTORS) 购买。

- [购买链接](https://www.nxp.com/design/designs/px4-robotic-drone-fmu-rddrone-fmuk66:RDDRONE-FMUK66#buy) (www.nxp.com)
- 遥测无线电根据频段单独购买：
  - [HGD-TELEM433](https://www.nxp.com/part/HGD-TELEM433)
  - [HGD-TELEM915](https://www.nxp.com/part/HGD-TELEM915)

::: info
_RDDRONE-FMUK66_ FMU 也包含在完整的 HoverGames 无人机套件中：[KIT-HGDRONEK66](https://www.nxp.com/applications/solutions/industrial/aerospace-and-mobile-robotics/uavs-drones-and-rovers/nxp-hovergames-drone-kit-including-rddrone-fmuk66-and-peripherals:KIT-HGDRONEK66#buy)
:::

<!--
## 接口

[接口图]

## 引脚分配

[引脚分配表]

## 硬件规格

[硬件规格表]
-->

## 组装与调试

### 硬件连接
1. 将 DCDC 模块连接至电源模块
2. 通过 CAN 总线连接各传感器模块
3. 使用 JTAG 接口连接调试器

### 软件配置
```bash
make px4_fmu-v6_default upload
```

## 系统要求

- 操作系统：Ubuntu 20.04 LTS
- 内存：8GB RAM 或更高
- 存储：50GB 可用空间
- 显卡：支持 OpenGL 3.3

## 常见问题

### 1. 无法启动 PX4 固件？
- 检查 USB 连接是否正常
- 确认开发板已正确配置为 Bootloader 模式
- 尝试重装 STM32 Flash Loader

### 2. GPS 信号丢失？
- 检查天线连接
- 确认 GPS 模块供电正常
- 使用 QGroundControl 进行信号强度测试

## 技术支持

遇到无法解决的问题时，请通过 [NXP 技术支持论坛](https://community.nxp.com/) 联系技术支持团队，或发送邮件至 support@nxp.com。

![HoverGamesDronelogo](../../assets/flight_controller/nxp_rddrone_fmuk66/hovergames_colored_small.png)