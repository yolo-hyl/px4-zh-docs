# 指南针电源补偿

指南针（磁强计）应尽可能远离承载大电流的电缆安装，因为这些电缆会产生可能干扰指南针读数的磁场。

本主题解释了在无法移动指南针位置时如何补偿这些感应磁场。

:::tip
将指南针移开电源线是解决此问题最简单且最有效的方法，因为磁场强度随与电缆的距离平方成反比衰减。
:::

::: info
该过程以多旋翼为例进行演示，但同样适用于其他机体类型。
:::

<a id="when"></a>

## 何时需要电源补偿？

只有以下所有条件同时满足时才建议执行此电源补偿：

1. 指南针无法远离电源线
1. 指南针读数与推力设定值和/或电池电流存在强相关性

   ![损坏的磁力计数据](../../assets/advanced_config/corrupted_mag.png)

1. 机体电缆固定不动（如果电流线缆可移动，计算得到的补偿参数将失效）

<a id="how"></a>

## 如何补偿指南针

1. 确保机体运行支持电源补偿的固件版本（当前master分支或v.1.11.0及以后版本）
1. 执行[标准指南针校准](../config/compass.md#compass-calibration)
1. 将参数[SDLOG_MODE](../advanced_config/parameter_reference.md#SDLOG_MODE)设置为2以启用启动日志记录
1. 将参数[SDLOG_PROFILE](../advanced_config/parameter_reference.md#SDLOG_PROFILE)的_传感器对比_（bit 6）复选框设置为获取更多数据点
1. 固定机体使其无法移动，并安装螺旋桨（使电机能抽取与飞行时相同的电流）
   本例使用绑带固定机体

   ![绑带固定](../../assets/advanced_config/strap.png)

1. 给机体供电并切换至[ACRO飞行模式](../flight_modes_mc/acro.md)（使用此模式可确保机体不会因绑带产生补偿动作）

   - 解锁机体并缓慢将油门推至最大
   - 缓慢将油门降至零
   - 锁定机体

   ::: info
   请谨慎执行测试并密切监控振动情况。
   :::

1. 取回ulog文件并使用python脚本[mag_compensation.py](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/sensors/vehicle_magnetometer/mag_compensation/python/mag_compensation.py)识别补偿参数

   ```sh
   python mag_compensation.py ~/path/to/log/logfile.ulg <type> [--instance <number>]
   ```

   其中：
   
      - `<type>`: `current` 或 `thrust`（用于补偿的电源信号）
      - `--instance <number>`（可选）：数字为`0`（默认）或`1`，表示使用的电流或推力信号实例

   ::: info
   如果日志中不包含电池电流测量值，需要注释掉Python脚本中对应的行，使其仅进行推力计算。
   :::

1. 脚本将返回推力和电流的参数识别结果并打印到控制台
   弹出的图形显示每个指南针实例的"拟合优度"，以及使用建议值补偿后的数据效果
   如果有电流测量值，使用电流补偿通常能得到更好结果
   以下是示例日志，其中电流拟合良好，但推力参数不可用，因为关系非线性

   ![线性拟合](../../assets/advanced_config/line_fit.png)

1. 参数识别完成后，通过将[CAL_MAG_COMP_TYP](../advanced_config/parameter_reference.md#CAL_MAG_COMP_TYP)设置为1（使用推力参数）或2（使用电流参数）启用电源补偿
   此外，还需为每个指南针的每个轴设置补偿参数

   ![补偿参数](../../assets/advanced_config/comp_params.png)