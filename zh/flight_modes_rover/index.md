# 驱动模式

飞行模式（或更准确地说，地面车辆的"驱动模式"）提供了自动驾驶仪支持，便于手动驾驶机体或执行自主任务。

本节列出了所有支持的驱动模式：[地面车辆](../frames_rover/index.md)。

关于将遥控器开关映射到特定模式的更多信息请参见：[基础配置 > 飞行模式](../config/flight_mode.md)。

::: warning
选择以下列出模式之外的任何模式，都可能导致机体停止运行或产生未定义行为。
:::

## 手动模式

| 模式                                  | 描述                                                                                                                                                                      |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [手动](manual.md#manual-mode)           | 无自动驾驶仪支持。用户需负责保持地面车辆在预定航路上，并控制速度和转向速率。                                                                                                                   |
| [特技](manual.md#acro-mode)             | + 维持偏航率（操作感觉更像驾驶汽车而非手动模式）。<br>+ 允许限制最大偏航率（防止侧翻）。                                                                                                       |
| [稳定](manual.md#stabilized-mode)       | + 维持偏航方向（保持直线性能显著优于手动模式）。                                                                                                                                           |
| [位置](manual.md#position-mode)         | + 维持航向（最佳直线行驶模式）。<br>+ 可抵抗干扰保持速度，例如爬坡时。<br>+ 允许限制最大速度。                                                                                               |

## 自动模式

| 模式                            | 描述                                                             |
| ------------------------------- | ----------------------------------------------------------------------- |
| [任务](auto.md#mission-mode)    | 自动模式，使机体执行预定义任务。                                                                                           |
| [返航](auto.md#return-mode)     | 自动模式，使机体返回发射位置。                                                                                             |