# BetaFPV Beta75X 2S Brushless Whoop

<Badge type="info" text="已停产" />

:::warning
此框架已[停产](../flight_controller/autopilot_experimental.md)，目前不再商业销售。
:::

[BetaFPV Beta75X](https://betafpv.com/products/beta75x-2s-whoop-quadcopter) 是一款体积非常小的四旋翼飞行器，既可室内也可室外飞行，支持FPV和目视飞行。

![BetaFPV Beta75X](../../assets/hardware/betafpv_beta75x.jpg)

## 购买渠道

_Beta75X_ 可通过以下供应商购买：

- [GetFPV](https://www.getfpv.com/beta75x-2s-brushless-whoop-micro-quadcopter-xt30-frsky.html)
- [Amazon](https://www.amazon.com/BETAFPV-Beta75X-Brushless-Quadcopter-Smartaudio/dp/B07H86XSPW)

此外您还需要：

- 一个遥控发射机。_Beta75X_ 可搭配多种接收机。PX4兼容所有版本，但需确保选择与您的发射机匹配的版本。
- 锂电充电器（机体标配一个电池，但建议准备备用电池）。
- FPV眼镜（如需FPV飞行）。  
  兼容选项众多，包括来自[Fatshark](https://www.fatshark.com/product-page/dominator-v3)的这些型号。

  ::: info
  FPV支持完全独立于PX4/飞控系统。
  :::

## 安装PX4引导程序

_Beta75X_ 预装Betaflight固件。

在加载PX4固件前，必须首先安装PX4引导程序。  
引导程序安装指南请参考[Omnibus F4](../flight_controller/omnibus_f4_sd.md#bootloader)主题（这是_Beta75X_使用的飞控板）。

:::tip
如需重新安装Betaflight，[随时可以执行此操作](../advanced_config/bootloader_update_from_betaflight.md#reinstall-betaflight)！
:::

## 安装/配置

引导程序安装完成后，您应能通过USB线将机体连接到_QGroundControl_。

::: info
撰写本文时，_Omnibus F4_ 仅支持QGroundControl的_Daily Build_版本，且仅提供主分支的预构建固件（稳定版本尚未发布）。
:::

安装并配置PX4的步骤如下：

- [加载PX4固件](../config/firmware.md)。
- [设置机型](../config/airframe.md)为 _BetaFPV Beta75X 2S Brushless Whoop_。
- 继续进行[基础配置](../config/index.md)，包括传感器校准和遥控器设置。

## 视频

<lite-youtube videoid="_-O0kv0Qsh4" title="PX4 running on the BetaFPV Whoop"/>