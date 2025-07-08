# 高级飞行控制器方向调校

这些说明可用于手动微调方向和地平线水平，例如纠正传感器板的轻微错位或微小校准误差。

如果存在持续的偏移偏差（常见于多旋翼飞行器但不仅限于此类），建议使用这些微调偏移角参数进行修正，而不是使用遥控器的微调功能。这可确保机体在完全自主飞行时仍能保持校准后的状态。

::: info
这些说明属于"高级"内容，不推荐普通用户使用（常规调校通常已足够）。
:::

## 设置方向参数

[SENS_BOARD_ROT](../advanced_config/parameter_reference.md#SENS_BOARD_ROT) 参数定义了飞行控制器板相对于机体坐标系的旋转，而微调偏移量 ([SENS_BOARD_X_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_X_OFF), [SENS_BOARD_Y_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_Y_OFF), [SENS_BOARD_Z_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_Z_OFF)) 则设置传感器相对于电路板本身的旋转。微调偏移量会被加到 `SENS_BOARD_ROT` 角度上，以确定飞行控制器的偏航、俯仰和翻滚方向的总偏移角度。

首先进行常规校准 [飞行控制器方向](../config/flight_controller_orientation.md) 和 [水平地平线校准](../config/level_horizon_calibration.md) 以设置 [SENS_BOARD_ROT](../advanced_config/parameter_reference.md#SENS_BOARD_ROT) 参数。

随后可通过设置其他参数来微调IMU传感器相对于电路板本身的方向。

您可在QGroundControl中按照以下路径定位这些参数：

1. 打开QGroundControl菜单：**设置 > 参数 > 传感器校准**。
1. 参数位于如下所示的章节中（或可通过搜索功能查找）：

   ![FC Orientation QGC v2](../../assets/qgc/setup/sensor/fc_orientation_qgc_v2.png)

## 参数汇总

- [SENS_BOARD_ROT](../advanced_config/parameter_reference.md#SENS_BOARD_ROT): FMU电路板相对于机体坐标系的旋转。
- [SENS_BOARD_X_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_X_OFF): 围绕PX4FMU的X轴（翻滚轴）的旋转角度（度）。
  正角度表示逆时针方向，负角度表示顺时针方向。
- [SENS_BOARD_Y_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_Y_OFF): 围绕PX4FMU的Y轴（俯仰轴）的旋转角度（度）。
  正角度表示逆时针方向，负角度表示顺时针方向。
- [SENS_BOARD_Z_OFF](../advanced_config/parameter_reference.md#SENS_BOARD_Z_OFF): 围绕PX4FMU的Z轴（偏航轴）的旋转角度（度）。
  正角度表示逆时针方向，负角度表示顺时针方向。