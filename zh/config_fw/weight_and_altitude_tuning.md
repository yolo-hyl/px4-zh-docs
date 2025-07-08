# 高级 TECS 调参（重量与高度）

本主题介绍了如何对[机体重量补偿](#vehicle-weight-compensation)和[空气密度补偿](#air-density-compensation)进行调整，并提供了有关[补偿算法](#weight-and-density-compensation-algorithms)的说明。

:::warning
本主题要求您已执行过[基础 TECS 调参](../config_fw/position_tuning_guide_fixedwing.md#tecs-tuning-altitude-and-airspeed)。
:::

[基础 TECS 调参](../config_fw/position_tuning_guide_fixedwing.md#tecs-tuning-altitude-and-airspeed)确定了机体在高度和空速控制器正常工作所需的性能限制。

虽然这些限制是通过常量参数指定的，但实际上机体性能并非恒定，会受到各种因素的影响。如果未考虑重量和空气密度的变化，当配置（空气密度和重量）与调参时的配置存在显著偏差时，高度和空速跟踪性能很可能会下降。

## 机体重量补偿

设置以下两个参数以调整最大爬升率、最小下降率，并调节空速限制与重量相关：

- [WEIGHT_BASE](../advanced_config/parameter_reference.md#WEIGHT_BASE) — 进行[基本TECS调参](../config_fw/position_tuning_guide_fixedwing.md#tecs-tuning-altitude-and-airspeed)时的机体重量。
- [WEIGHT_GROSS](../advanced_config/parameter_reference.md#WEIGHT_BASE) — 机体在任意时刻的实际重量，例如使用更大电池或携带未在调参时存在的载荷时的重量。

您可以通过在调参配置下使用电子秤测量机体重量，以及携带载荷飞行时测量的重量来确定这些值。

当 `WEIGHT_BASE` 和 `WEIGHT_GROSS` 均大于 `0` 时会执行缩放操作，若两者数值相同则不会生效。
详见下文的[算法](#weight-and-density-compensation-algorithms)部分获取更多信息。

## 空气密度补偿

### 指定服务天花板

在PX4中，服务天花板[FW_SERVICE_CEIL](../advanced_config/parameter_reference.md#FW_SERVICE_CEIL)定义了机体在标准大气条件下仍能以最大油门和重量等于[WEIGHT_BASE](../advanced_config/parameter_reference.md#WEIGHT_BASE)时实现0.5 m/s最大爬升率的海拔高度。  
默认情况下该参数处于禁用状态，不会进行补偿。

该参数需要通过实验确定。  
设定保守值（较低值）总是优于设定乐观值。

### 应用密度校正到最小下沉率

最小下沉率通过[FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN)设定。

如果[基础TECS调参](../config_fw/position_tuning_guide_fixedwing.md#tecs-tuning-altitude-and-airspeed)未在标准海平面条件下进行，则必须通过乘以校正因子$P$（其中$\rho$为调参时的空气密度）调整[FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN)参数：

$$P = \sqrt{\rho\over{\rho_{sealevel}}}$$

更多信息请参阅[密度对最小下沉率的影响](#effect-of-density-on-minimum-sink-rate)。

### 应用密度校正到油门校正

油门校正通过[FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM)设定。

如果基础调参未在标准海平面条件下进行，则必须通过乘以校正因子$P$调整[FW_THR_TRIM](../advanced_config/parameter_reference.md#FW_THR_TRIM)参数：

$$P = \sqrt{\rho\over{\rho_{sealevel}}}$$

更多信息请参阅[密度对油门校正的影响](#effect-of-density-on-trim-throttle)

## 重量和密度补偿算法

本节包含PX4执行的缩放操作信息。
仅供感兴趣者参考，可能对希望修改缩放代码的开发者有用。

### 符号表示

在以下章节中，我们使用符号$\hat X$表示该值是变量$X$的校准值。
校准指在标准大气条件下海平面测量的变量值，且机体重量等于[WEIGHT_BASE](../advanced_config/parameter_reference.md#WEIGHT_BASE)时的值。

例如，$\hat{\dot{h}}_{max}$表示机体在[WEIGHT_BASE](../advanced_config/parameter_reference.md#WEIGHT_BASE)、标准大气条件海平面可达到的最大爬升率。

### 重量对最大爬升率的影响

最大爬升率([FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX))按重量比函数进行缩放。

从飞机的稳态运动方程可知，最大爬升率可表示为：

$$\dot{h}_{max} = { V * ( Thrust - Drag ) \over{m*g}}$$

其中`V`是真实空速，`m`是机体质量。
从该公式可以看出最大爬升率与机体质量成比例关系。

### 重量对最小下沉率的影响

最小下沉率([FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN))按重量比函数进行缩放。

最小下沉率可表示为：

$$\dot{h}_{min} = \sqrt{2mg\over{\rho S}} f(C_L, C_D)$$

其中$\rho$是空气密度，S是机翼参考面积，$f(C_L, C_D)$是极曲线、升力和阻力的函数。

从该公式可见，最小下沉率与重量比的平方根成比例。

### 重量对空速限制的影响

最小空速([FW_AIRSPD_MIN](../advanced_config/parameter_reference.md#FW_AIRSPD_MIN))、失速空速([FW_AIRSPD_STALL](../advanced_config/parameter_reference.md#FW_AIRSPD_STALL))和配平空速([FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM))根据[WEIGHT_BASE](../advanced_config/parameter_reference.md#WEIGHT_BASE)和[WEIGHT_GROSS](../advanced_config/parameter_reference.md#WEIGHT_GROSS)指定的重量比进行调整。

在稳态飞行中，我们可以要求升力等于机体重量：

$$Lift = mg = {1\over{2}} \rho V^2 S C_L$$

将该方程重新整理为空速表达式：

$$V = \sqrt{\frac{2mg}{\rho S C_D }}$$

从该公式可见，假设攻角恒定（这是我们通常期望的），机体重量与空速呈平方根关系。
因此，上述空速限制均按重量比的平方根进行缩放。

### 密度对最大爬升率的影响

最大爬升率通过[FW_T_CLMB_MAX](../advanced_config/parameter_reference.md#FW_T_CLMB_MAX)设置。

如前所述，最大爬升率可表示为：

$$\dot{h}_{max} = { V * ( Thrust - Drag ) \over{m*g}}$$

空气密度影响空速、推力和阻力，其建模并不直观。
然而，文献和经验表明，对于螺旋桨飞机，最大爬升率随空气密度线性下降。
因此，最大爬升率可表示为：

$$\dot{h}_{max} = \hat{\dot{h}} * {\rho_{sealevel} \over{\rho}} K$$

其中$\rho_{sealevel}$是标准大气海平面空气密度，K是决定函数斜率的缩放因子。
通常航空实践中不尝试识别这些常数，而是指定服务天花板高度，即机体仍能实现最小指定爬升率的海拔高度。

### 密度对最小下沉率的影响

最小下沉率通过[FW_T_SINK_MIN](../advanced_config/parameter_reference.md#FW_T_SINK_MIN)设置。

在前几节我们已看到最小下沉率公式：

$$\dot{h}_{min} = \sqrt{2mg\over{\rho S}} f(C_L, C_D)$$

这表明最小下沉率与空气密度倒数的平方根成比例。

### 密度对配平油门的影响

待办事项：此处添加推导过程。