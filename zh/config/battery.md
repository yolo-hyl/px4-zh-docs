# 电池估算调节（电源设置）

本主题说明如何配置电源设置，以便 PX4 可估算可用电池容量。

::: info
这些说明要求机体配备[电源模块（PM）](../power_module/index.md)或可测量电池电压（及可选电流）的其他硬件。
:::

::: tip
此调节对[智能/MAVLink 电池](../smart_batteries/index.md)（可提供可靠剩余电量指示的电池）不需要。
:::

## 概述

电池估算调参使用测量的电压和电流（如可获得）来估算剩余电池容量。  
这一点很重要，因为它允许PX4在机体接近电量耗尽和可能发生坠毁时采取行动（同时防止电池因深度放电而损坏）。

PX4提供了多种（逐步增强的）方法用于估算容量：

1. [基本电池设置](#basic_settings)（默认）：直接比较测量电压与"空"和"满"电压范围。
   由于测量电压（及其对应的容量）在负载下会波动，因此估算结果较为粗略。
1. [带负载补偿的电压估算](#load_compensation)：抵消负载对容量计算的影响。
1. [带电流积分的电压估算](#current_integration)：将负载补偿后的电压估算与基于电流的已消耗电荷估算进行融合。
   此方法可获得与智能电池相当的容量估算精度。

后续方法在前序方法基础上扩展而来。
所采用的方法取决于机体的电源模块是否能够测量电流。

::: info
以下说明引用了电池1校准参数：`BAT1_*`。
其他电池使用`BATx_*`参数，其中`x`表示电池编号。
所有电池校准参数[详见此处](../advanced_config/parameter_reference.md#battery-calibration)。
:::

:::tip
除了此处讨论的PX4配置外，您还应确保电调的低压切断功能已禁用或设置在预期最低电压以下。
这可确保电池故障安全行为由PX4管理，并防止电调在电池仍有电量（根据您选择的"空电池"设置）时突然切断电源。
:::

:::tip
[电池化学特性概述](../power_systems/battery_chemistry.md)解释了主要电池类型之间的差异及其对电池设置的影响。
:::

## 基本电池设置（默认） {#basic_settings}

基本电池设置用于配置 PX4 以使用默认的电量估算方法。  
该方法通过将测量到的原始电池电压与"空"和"满"电池单元的电压范围进行比较（根据电池单元数量进行缩放）来估算电量。

::: info
由于负载变化导致测量电压波动，此方法会产生相对粗糙的估算结果。
:::

要配置电池1的基本设置：

1. 启动 _QGroundControl_ 并连接机体。
1. 选择 **"Q"图标 > 机体设置 > 电源** (侧边栏) 打开 _电源设置_。

系统将显示描述电池特性的基本设置参数。下文各部分将解释每个字段应设置的数值。

![QGC 电源设置](../../assets/qgc/setup/power/qgc_setup_power_px4.png)

::: info
截至撰写时 _QGroundControl_ 仅允许在此视图中设置电池1的参数。对于多电池机体，需要直接[设置参数](../advanced_config/parameters.md) 电池2（`BAT2_*`），如后续章节所述。
:::

### 电池串联单元数

此参数设置电池中串联的电池单元数量。通常电池上会标注数字加"S"（例如 "3S", "5S"）表示串联数。

::: info
单个电化学电池单元的电压取决于[电池类型](../power_systems/battery_chemistry.md)的化学特性。  
锂聚合物（LiPo）电池和锂离子（LiIon）电池的标称单元电压均为3.7V。  
为了获得更高的电压（更高效地驱动机体），多个电池单元会以_串联_方式连接。  
电池终端的电压则为单元电压的整数倍。
:::

若未提供电池单元数，可通过将电池电压除以单个电池单元的标称电压计算得出。下表展示了这些电池的电压-单元数关系：

| 单元数 | LiPo（V） | LiIon（V） |
| ----- | -------- | --------- |
| 1S    | 3.7      | 3.7       |
| 2S    | 7.4      | 7.4       |
| 3S    | 11.1     | 11.1      |
| 4S    | 14.8     | 14.8      |
| 5S    | 18.5     | 18.5      |
| 6S    | 22.2     | 22.2      |

::: info
此设置对应[参数](../advanced_config/parameters.md): [BAT1_N_CELLS](../advanced_config/parameter_reference.md#BAT1_N_CELLS) 和 [BAT2_N_CELLS](../advanced_config/parameter_reference.md#BAT2_N_CELLS)。
:::

### 满电电压（每单元）

此参数设置每个电池单元的_标称_最大电压（电池单元被判定为"满"的最低电压）。

该值应略低于电池的标称最大单元电压，但不能过低以至于飞行几分钟后估计电量仍为100%。

推荐使用的数值为：

- **LiPo:** 4.05V（_QGroundControl_ 中的默认值）  
- **LiIon:** 4.05V

::: info
满电电池的电压可能在充电后随时间略有下降。设置略低于最大值的补偿值可以抵消这种下降。
:::

::: info
此设置对应[参数](../advanced_config/parameters.md): [BAT1_V_CHARGED](../advanced_config/parameter_reference.md#BAT1_V_CHARGED) 和 [BAT2_V_CHARGED](../advanced_config/parameter_reference.md#BAT2_V_CHARGED)。
:::

### 空电电压（每单元）

此参数设置每个电池单元的标称最低安全电压（低于此电压可能会损坏电池）。

::: info
并没有一个统一的值来判定电池是否为空。  
若选择值过低，电池可能因过度放电（和/或机体坠毁）而损坏。  
若选择值过高，可能会不必要的缩短飞行时间。
:::

每单元最低电压的经验法则：

| 电压等级                                | LiPo（V） | LiIon（V） |
| --------------------------------------- | -------- | --------- |
| 保守值（无负载电压）                    | 3.7      | 3         |
| "真实"最低值（负载/飞行中电压）         | 3.5      | 2.7       |
| 损坏电池值（负载中电压）                | 3.0      | 2.5       |

:::tip
保守值以下，越早给电池充电越好 - 电池寿命越长且容量损失越慢。
:::

::: info
此设置对应[参数](../advanced_config/parameters.md): [BAT1_V_EMPTY](../advanced_config/parameter_reference.md#BAT1_V_EMPTY) 和 [BAT2_V_EMPTY](../advanced_config/parameter_reference.md#BAT2_V_EMPTY)。
:::

### 电压分压器

如果机体通过电源模块和飞控ADC测量电压，则需要为每个电源模块单独校准测量值。校准时需测量电池的实际电压（使用万用表）并与电源模块提供的数值进行比较，从而计算出"电压分压器"值，用于后续将电源模块测量值缩放为正确值。

通过 _QGroundControl_ 并按照[设置 > 电源设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/power.html) (QGroundControl 用户指南) 中的分步指南进行校准是最快捷的方式。

::: info
此设置对应参数: [BAT1_V_DIV](../advanced_config/parameter_reference.md#BAT1_V_DIV) 和 [BAT2_V_DIV](../advanced_config/parameter_reference.md#BAT2_V_DIV)。
:::

### 安培每伏特 {#current_divider}

:::tip
如果电源模块不提供电流测量，则不需要此校准。
:::

电流测量默认用于[负载补偿](#load_compensation)和[电流积分](#current_integration)（如果电源模块提供该数据）。必须校准安培每伏特分压器以确保电流测量准确。

通过 _QGroundControl_ 并按照[设置 > 电源设置](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/power.html) (QGroundControl 用户指南) 中的分步指南进行校准是最简便的方式。

::: info
此设置对应参数: [BAT1_A_PER_V](../advanced_config/parameter_reference.md#BAT1_A_PER_V) 和 [BAT2_A_PER_V](../advanced_config/parameter_reference.md#BAT2_A_PER_V)。
:::

## 电压估算与负载补偿 {#load_compensation}

当电流通过电池时，内部电阻会导致电压下降，使电池的实际输出电压低于其开路电压（无负载时的电压）。  
在使用[基础配置](#basic_settings)时，估算可用容量所依据的是测量到的输出电压，这意味着飞行过程中电池电量会随着飞行高度变化或负载改变而出现波动。

负载补偿通过测量或估算电池内部电阻值来修正负载下的变化，从而在飞行过程中显著减少估算容量的波动。  
当使用提供电流测量的电源模块时，此功能默认启用。

要使用负载补偿，首先设置[基础配置](#basic_settings)。  
_空电量压_ ([BATn_V_EMPTY](../advanced_config/parameter_reference.md#BAT1_V_EMPTY)，其中 `n` 为电池编号) 应设置得更高（相比未启用补偿时），因为补偿后的电压将用于估算（通常设置为略低于使用后电池静置时的单节电压）。

随后需要校准[电压分压电流](#current_divider)（在基础配置界面中校准，以获得可靠的电流测量）。

PX4 默认使用基于电流的负载补偿，通过电池内部电阻的实时估算来实现（若 [BAT1_R_INTERNAL=-1](../advanced_config/parameter_reference.md#BAT1_R_INTERNAL) 则启用实时估算）。  
实时估算可使补偿适应飞行过程中因温度变化导致的内部电阻变化，以及电池老化过程中的电阻变化。

内部电阻也可通过测量后[手动设置](../advanced_config/parameters.md)在 [BAT1_R_INTERNAL](../advanced_config/parameter_reference.md#BAT1_R_INTERNAL) 中指定。  
该参数中设置的正值将作为内部电阻值替代估算值（`0` 表示完全禁用负载补偿）。

:::info
某些LiPo充电器可以测量电池的内部电阻。  
LiPo电池的典型内部电阻值为每节5mΩ，但该值会随放电倍率、电池寿命和健康状态而变化。
:::

## 基于电压的估算与电流积分融合 {#current_integration}

此方法是测量相对电池消耗最准确的方式。  
如果每次启动时都正确设置并使用健康且新充好的电池，估算质量将与智能电池相当（理论上可实现精确的剩余飞行时间估算）。

该方法通过将基于电压的可用容量估算与基于电流的已消耗电荷估算进行融合来评估剩余电池容量。  
它需要能够精确测量电流的硬件。

要启用此功能：

1. 首先使用[负载补偿](#load_compensation)设置准确的电压估算。  
   
   :::tip  
   包括校准[Amps per volt divider](#current_divider)设置。  
   :::

2. 将参数[BAT1_CAPACITY](../advanced_config/parameter_reference.md#BAT1_CAPACITY)设置为约90%的电池标称容量（通常打印在电池标签上）。  
   
   ::: info  
   不要将此值设置得过高，否则可能导致估算不准确或估算容量突然下降。  
   :::

---

**附加信息**

通过数学积分测量的电流来估算随时间消耗的电荷（此方法提供非常精确的能耗估算）。

系统启动时，PX4首先使用基于电压的估算来确定初始电池电量。然后将此估算值与电流积分的值融合，提供更精确的综合估算。  
融合结果中每个估算值的相对权重取决于电池状态。电池越空，电压估算的权重越大。这可以防止深度放电（例如由于容量配置错误或初始值错误）。

如果始终从健康的满电电池开始，此方法与智能电池的使用方式类似。

::: info  
电流积分无法单独使用（不基于电压估算），因为它无法确定初始容量。  
电压估算可以估算初始容量并提供可能错误的持续反馈（例如电池故障，或通过不同方法计算的容量不匹配）。  
:::