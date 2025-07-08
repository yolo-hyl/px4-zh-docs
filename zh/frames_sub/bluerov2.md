# BlueROV2（UUV）

<Badge type="tip" text="PX4 v1.12" />

[BlueROV2](https://bluerobotics.com/store/rov/bluerov2-upgrade-kits/brov2-heavy-retrofit-r1-rp/BlueROV2) 是一款高性能且价格合理的水下无人航行器，适用于检查、研究和探索任务。

PX4 为 8 推力矢量配置（称为 _BlueROV2 Heavy Configuration_）提供[实验性支持](index.md)。

![Hero](../../assets/airframes/sub/bluerov/bluerov_hero.jpg)

## 购买渠道

[BlueROV2](https://bluerobotics.com/store/rov/bluerov2/) + [BlueROV2 Heavy Configuration Retrofit Kit](https://bluerobotics.com/store/rov/bluerov2-upgrade-kits/brov2-heavy-retrofit-r1-rp/)

### 电机映射/接线

电机必须按照 BlueRobotics 提供的标准接线指南连接到飞控器。

机体将匹配[机型参考文档](../airframes/airframe_reference.md#vectored-6-dof-uuv)中记录的配置：

<img src="../../assets/airframes/types/Vectored6DofUUV.svg" width="29%" style="max-height: 180px;"/>

- **MAIN1:** 电机1 CCW，船首右舷水平方向，螺旋桨 CCW
- **MAIN2:** 电机2 CCW，船首左舷水平方向，螺旋桨 CCW
- **MAIN3:** 电机3 CCW，船尾右舷水平方向，螺旋桨 CW
- **MAIN4:** 电机4 CCW，船尾左舷水平方向，螺旋桨 CW
- **MAIN5:** 电机5 CCW，船首右舷垂直方向，螺旋桨 CCW
- **MAIN6:** 电机6 CCW，船首左舷垂直方向，螺旋桨 CW
- **MAIN7:** 电机7 CCW，船尾右舷垂直方向，螺旋桨 CW
- **MAIN8:** 电机8 CCW，船尾左舷垂直方向，螺旋桨 CCW

## 机型配置

BlueROV2 本身不预装 PX4。您需要：

1. [安装 PX4 固件](../config/firmware.md#installing-px4-main-beta-or-custom-firmware)
1. [设置机型](../config/airframe.md)。
   需选择 "BlueROV2 Heavy Configuration"，如下图所示：
   ![QGC - 为 BlueROV2 Heavy 配置选择机型](../../assets/airframes/sub/bluerov/qgc_airframe.jpg)

<!-- 其他调参/测试等？ -->

## 视频

<lite-youtube videoid="1sUaURmlmT8" title="PX4 on BlueRov Demo"/>

<!-- @DanielDuecker 在 GitHub 上是了解该机型的合适联系人 -->