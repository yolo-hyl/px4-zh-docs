# CUAV HV PM（高压电源模块）

CUAV<sup>&reg;</sup> *HV_PM* 电源模块是由 CUAV 独立开发的“高压”电源模块。

:::tip
*HV_PM* 包含在 CUAV V5+/V5 nano 套件中，但也可以单独出售。  
根据飞控类型（Pixhack v3, V5+/V5 nano, Pixhawk）不同，使用的线缆也不同。  
它可与其它飞控配合使用，但可能需要修改线缆引脚。
:::

## 规格参数

- **更高电压输入：** 10V-60V（3s~14s电池）
- **精准电池监测：**  
  - **电压检测精度：** ±0.1V  
  - **电流检测精度：** ±0.2A
- **BEC（5V）最大电流：** 5A
- **最大（检测）电流：** 60A
- **最大输出电流（电调/电机接口）：** 60A

## 购买渠道

[CUAV aliexpress 店铺](https://www.aliexpress.com/item/32841805115.html?spm=2114.12010615.8148356.1.64165998hPvTKQ)

## 引脚定义

![HV PM](../../assets/hardware/power_module/cuav_hv/hv_pm.jpg)

## 启用 HV PM

[电池估算调校](../config/battery.md) 介绍了如何配置电池和电源模块。

*HV_PM* 的关键配置参数为：
- **电压分压器：** 18
- **每伏安培数：** 24 A/V