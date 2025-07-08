# TFSLOT - 文丘里效应空速传感器

[TFSLOT](https://github.com/ThunderFly-aerospace/TFSLOT01) 是一个基于 [文丘里效应](https://en.wikipedia.org/wiki/Venturi_effect) 的开源空速传感器，同时集成有IMU模块。

![TFSLOT 和 TFSLOT WITH TFASPDIMU02 板](../../assets/hardware/sensors/airspeed/tsflot_compose.jpg)

[TFSLOT](https://github.com/ThunderFly-aerospace/TFSLOT01) 是基于文丘里效应的空速传感器。在基础配置中，TFSLOT 配备了 [TFASPDIMU02](https://github.com/ThunderFly-aerospace/TFASPDIMU02) 传感器板，该板包含一个差压传感器 ([Sensirion SDP3x 系列](https://sensirion.com/products/catalog/?filter_series=d1816d53-f5c8-47e3-ab47-818c3fd54259)) 和一个九轴运动追踪传感器 ([ICM-20948](https://invensense.tdk.com/products/motion-tracking/9-axis/icm-20948/))。IMU 模块可作为外部磁力计使用。

- 该设计在小型和低速无人机上具有多种优势。
- 低空速（低于10 m/s）时具有更高分辨率。
- 通过改变轮廓可配置灵敏度。
- 堵塞倾向更小（例如着陆后因泥浆堵塞）。
- 防水（雨雪等）。
- 直接集成差压传感器，无需额外软管。
  传感器故障概率更低。
- 可直接集成到无人机结构中。
  设计完全开源。
- 集成外部 IMU 模块。

得益于打印管的设计，测量轮廓非常容易更换，从而在特定速度范围内调整灵敏度。在基础形式中，其优化使得测得的差压对应于皮托管的压力。

![TFSLOT 集成在 TF-G2 中](../../assets/hardware/sensors/airspeed/tfslot_integration.jpg)
_TFSLOT 在 [TF-G2](https://github.com/ThunderFly-aerospace/TF-G2/) 自转旋翼机中的首次集成_

::: info

完整文档和源文件可在 [GitHub](https://github.com/ThunderFly-aerospace/TFSLOT01) 找到。

:::

## 购买渠道

TFSLOT 可在 [Tindie 商店](https://www.tindie.com/products/thunderfly/tfslot01a-inovative-drone-airspeed-sensor/) 购买，或通过发送电子邮件至 info@thunderfly.cz 进行咨询。

## 连接

[TFASPDIMU02](https://github.com/ThunderFly-aerospace/TFASPDIMU02) 配备 I2C JST-GH 接口，符合 [dronecode 标准](https://github.com/pixhawk/Pixhawk-Standards/blob/master/DS-009%20Pixhawk%20Connector%20Standard.pdf) 的引脚定义。因此，传感器可通过 I2C 4针 JST-GH 电缆直接连接到飞控的 I2C 接口。

## 配置

由于传感器前端连接了 [IMU IC](https://invensense.tdk.com/products/motion-tracking/9-axis/icm-20948/)，需要将 IMU IC 设置为桥接模式。随后可以运行空速传感器驱动。可通过以下命令序列实现，假设连接到 I2C2 接口。

```sh
icm20948_i2c_passthrough start -X -b 2 -a 0x68
sdp3x_airspeed start -X -b 2
```

该命令序列可存储在 SD 卡的 `/etc/config.txt` 文件中。关于 SD 卡配置的更多信息，请参阅 [单独页面](../concept/system_startup.md#replacing-the-system-startup)。

由于差压转换为速度的方式与皮托管不同，需要更改此配置文件。通过将参数 [`CAL_AIR_CMODEL`](../advanced_config/parameter_reference.md#CAL_AIR_CMODEL) 设置为 3（基于文丘里效应的空速传感器）来实现。

## 校准

校准较为困难，因为当前固件版本不支持负值校准。由于使用的传感器对气流方向对称测量且具有零点偏移，因此无需每次起飞前重复校准。但必须确保校准过程中无气流干扰。

最简单的校准方法是使用胶带将传感器的压力入口粘贴密封。然后启动校准过程，并在提示时从背面吹气。如果产生至少 50 Pa 的压力，校准将成功。