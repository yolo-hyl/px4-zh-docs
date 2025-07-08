# PWM限制状态机

`PWM_limit State Machine` 根据预启动和启动输入控制PWM输出。  
在启动信号激活后，提供从启动状态到油门逐渐增加之间的延迟。

## 快速概览

**输入**

- armed：激活以启用危险行为（如螺旋桨旋转）
- pre-armed：激活以启用安全行为（如移动控制面）
- 该输入会覆盖当前状态
- 预启动信号的激活会立即强制状态行为为ON，无论当前状态如何
- 预启动信号的解除激活会将行为恢复到当前状态

**状态**

- INIT 和 OFF  
  - PWM输出设置为未启动值。
- RAMP  
  - PWM输出从未启动值逐渐增加到最小值。
- ON  
  - PWM输出根据控制值设置。

## 状态转换图

![PWM Limit状态机图](../../assets/diagrams/pwm_limit_state_diagram.svg)