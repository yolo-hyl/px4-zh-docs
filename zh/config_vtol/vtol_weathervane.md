# VTOL风向标功能

**_风向标_**功能可在悬停飞行时自动调整VTOL机体朝向，使其鼻部正对相对风向。  
这能提升稳定性（减少侧风对迎风面机翼的冲击导致机体翻转的风险）。

该功能在混合式VTOL机体以多旋翼模式飞行时[默认启用](#配置)。

::: info
纯多旋翼不支持风向标功能。
:::

## 手动模式行为

风向标功能仅在[位置模式](../flight_modes_mc/position.md)（非其他手动多旋翼模式）下生效。  

当风向标控制器试图将机体鼻部对准风向时，用户仍可通过偏航杆请求偏航率。  
目标偏航率由风向标偏航率与用户指令偏航率共同决定。

## 任务模式行为

在[任务模式](../flight_modes_vtol/mission.md)下，当参数启用时风向标功能将始终生效。  
任务中任何偏航角度指令都将被忽略。

<a id="configuration"></a>

## 配置

该功能通过[WV\_\* 参数](../advanced_config/parameter_reference.md#WV_EN)进行配置。

| 参数 | 描述 |
|------|------|
| [WV_EN](../advanced_config/parameter_reference.md#WV_EN) | 启用风向标功能。 |
| [WV_ROLL_MIN](../advanced_config/parameter_reference.md#WV_ROLL_MIN) | 风向标控制器请求偏航率的最小滚转角设定值。 |
| [WV_YRATE_MAX](../advanced_config/parameter_reference.md#WV_YRATE_MAX) | 风向标控制器可请求的最大偏航率。 |

## 工作原理

悬停飞行时，机体需克服风力对其的阻力以保持位置。  
实现方式是将推力矢量倾斜至相对风向（即"迎风倾斜"）。  
通过追踪推力矢量可估算风向。  
风向标控制器用于发出偏航率指令，使机体鼻部对准估算的风向。