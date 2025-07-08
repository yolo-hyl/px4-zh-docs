# OneShot 电调和舵机（电机控制器）

PX4 仅支持 OneShot 125 电调。  
它们通常比 [PWM 电调](../peripherals/pwm_escs_and_servo.md) 更快且响应更灵敏，但接线方式相同（只需设置不同参数即可）。

::: info  
[DShot](../peripherals/dshot.md) 应优先于 OneShot 使用（如果硬件支持），因为其响应更快、稳定性更强、无需校准，且可能支持遥测功能。  
唯一不使用 DShot 的原因是硬件限制（可用 DShot 引脚不足或使用不支持 DShot 的电调）。  
:::

## 概述  

OneShot 本质上是 [PWM](../peripherals/pwm_escs_and_servo.md) 的变体，理论上速度可达 8 倍。  

PWM 和 OneShot 都通过周期性脉冲进行通信，脉宽表示所需功率水平。  
PWM 的脉宽通常在 1000us（零功率）到 2000us（满功率）之间，而 OneShot 125 的脉宽为 1/8，范围为 125us（零功率）到 250us（满功率）。  

脉冲发送的理论最大频率（即响应速度）取决于最大脉宽。  
PWM 的频率接近 500Hz，而 OneShot 接近 4kHz。  
实际上，OneShot 电调的最大频率通常在 1kHz 到 2kHz 之间，具体取决于所用电调型号。  

## 配置  

### 接线  

接线方式与 [PWM 电调](../peripherals/pwm_escs_and_servo.md)（以及 DShot）完全相同。  

### PX4 配置  

启用 OneShot 时，在 [执行器配置](../config/actuators.md) 中为输出组选择协议。  
注意输出范围值需设置为常规 PWM 范围（通常为 `1000` 到 `2000`）。  
这些值会内部转换为适用于 OneShot 的脉宽。  

然后执行 [电调校准](../advanced_config/esc_calibration.md)。