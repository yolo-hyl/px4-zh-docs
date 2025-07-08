# CUAV C-RTK

[CUAV C-RTK GPS接收机](https://www.cuav.net/en/c_rtk_9ps/) 是一款面向大众市场的[RTK GPS模块](../gps_compass/rtk_gps.md)。完整的RTK系统至少需要两个C-RTK模块（一个用于基准站，其他用于飞行器）。通过RTK，PX4可以获得厘米级定位精度，比普通GPS的定位精度高得多。

<img src="../../assets/hardware/gps/rtk_c-rtk.jpg" width="500px" title="C-RTK" />

## 购买渠道

- [cuav淘宝](https://item.taobao.com/item.htm?id=565380634341&spm=2014.21600712.0.0)
- [cuav速卖通](https://www.aliexpress.com/store/product/CUAV-NEW-Flight-Controller-GPS-C-RTK-differential-positioning-navigation-module-GPS-for-PIX4-Pixhawk-pixhack/3257035_32853894248.html?spm=2114.12010608.0.0.75592fadQKPPEn)

## 配置

通过_QGroundControl_在PX4上配置和使用RTK主要为即插即用（更多信息请参见[RTK GPS](../gps_compass/rtk_gps.md)）。

## 接线与连接

C-RTK GPS配有6针和4针接口，兼容[Pixhack v3](https://doc.cuav.net/flight-controller/pixhack/en/quick-start-pixhack-v3x.html#gps--compass)。6针接口用于RTK GPS，应连接到飞控的GPS端口。4针接口是m8n（标准）GPS接口，可作为（可选）第二GPS使用。

:::tip
撰写本文时，PX4尚未完全支持第二GPS。4针接口无需连接。
:::

<img src="../../assets/hardware/gps/rtk_cuav_c-rtk_to_6pin_connector.jpg" width="500px" title="C-RTK_6PIN" />

可能需要修改线缆/接口以连接到其他飞控板。Pixhawk 3 Pro和Pixracer的引脚映射如下所示。

### 引脚分配

C-RTK GPS的引脚分配如下所示。可用于修改其他飞控板的接口。

| pin | C-RTK GPS 6P | pin | Pixhawk 3 Pro GPS | C-RTK GPS 4P |
| --- | ------------ | --- | ----------------- | ------------ |
| 1   | SDA          | 1   | VCC               |              |
| 2   | SCL          | 2   | GPS_TX            |              |
| 3   | GPS_RX       | 3   | GPS_RX            | GPS_RX       |
| 4   | GPS_TX       | 4   | SCL               | GPS_TX       |
| 5   | VCC_5V       | 5   | SDA               | VCC_5v       |
| 6   | GND          | 6   | GND               | GND          |