# 空速校准

::: info
[Airspeed sensors](../sensor/airspeed.md) are highly recommended for Fixed-wing and VTOL vehicles.
:::

::: warning
Unlike most other sensor drivers, the airspeed sensor drivers are not automatically started.
Before calibration they must be [enabled via the corresponding parameter](../advanced_config/parameters.md):

- Sensirion SDP3X ([SENS_EN_SDP3X](../advanced_config/parameter_reference.md#SENS_EN_SDP3X))
- TE MS4525 ([SENS_EN_MS4525DO](../advanced_config/parameter_reference.md#SENS_EN_MS4525DO))
- TE MS5525 ([SENS_EN_MS5525DS](../advanced_config/parameter_reference.md#SENS_EN_MS5525DS))
- Eagle Tree airspeed sensor ([SENS_EN_ETSASPD](../advanced_config/parameter_reference.md#SENS_EN_ETSASPD))
:::

## 执行校准

要校准空速传感器：

1. 启动 _QGroundControl_ 并连接机体。
1. 如果尚未完成，请启用空速传感器（如上文 _WARNING_ 所述）。
1. 选择 **"Q"图标 > 机体设置 > 传感器** (侧边栏) 以打开 _传感器设置_。
1. 点击 **空速** 传感器按钮。

   ![Airspeed calibration](../../assets/qgc/setup/sensor/sensor_airspeed.jpg)

1. 用身体遮挡传感器避免风的影响（例如用手遮挡），注意不要堵住其任何孔洞。
1. 点击 **OK** 开始校准。
1. 当提示时向皮托管尖端吹气以标记校准结束。

   ::: tip
   向管内吹气也是检查动压口和静压口是否正确安装的基本方法。
   如果二者接反，传感器在吹气时会读取到大负压差，校准将因错误中止。
   :::

1. _QGroundControl_ 会告知您校准是否成功。

## 补充信息

- [QGroundControl User Guide > Sensors](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/sensors_px4.html#airspeed)