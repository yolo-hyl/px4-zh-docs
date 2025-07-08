# Holybro Kopis 2

[Holybro Kopis 2](https://holybro.com/products/kopis2-hdv-free-shipping) 是一款即飞竞速四旋翼飞行器，适用于FPV或目视飞行。

![Kopis 2](../../assets/hardware/holybro_kopis2.jpg)

## 购买渠道

_Kopis 2_ 可从以下供应商购买：

- [Holybro](https://holybro.com/products/kopis2-hdv-free-shipping) <!-- item code 30069, 30070 -->
- [GetFPV](https://www.getfpv.com/holybro-kopis-2-6s-fpv-racing-drone-pnp.html)

此外还需要：

- 一个遥控发射器。_Kopis 2_ 可选择配备 FrSky 接收器或无接收器。
- 锂聚合物电池和充电器。
- 如果需要进行FPV飞行，需要FPV眼镜。
  有许多兼容选项，包括来自[Fatshark](https://www.fatshark.com/product-page/dominator-v3)的这些产品。
  如果您拥有Kopis 2的HDV版本，也可以使用DJI FPV眼镜。

  ::: info
  FPV支持与PX4/飞控系统完全独立。
  :::

## 烧录PX4引导程序

_Kopis 2_ 预装了Betaflight。

在加载PX4固件之前，必须首先安装PX4引导程序。
安装引导程序的说明请参考 [Kakute F7](../flight_controller/kakutef7.md#bootloader) 话题（这是_Kopis 2_上的飞控主板）。

:::tip
如果需要，您可以随时[重新安装Betaflight](../advanced_config/bootloader_update_from_betaflight.md#reinstall-betaflight)！
:::

## 安装/配置

安装引导程序后，您应该可以通过USB线将机体连接到_QGroundControl_。

::: info
撰写本文时，_Kopis 2_ 支持QGroundControl _每日构建版_，并且仅为主分支提供预构建固件（稳定版尚未可用）。
:::

要安装和配置PX4：

- [加载PX4固件](../config/firmware.md)。
- [设置机体类型](../config/airframe.md) 为 _Holybro Kopis 2_。
- 继续进行[基础配置](../config/index.md)，包括传感器校准和遥控器设置。