# RaccoonLab Mini/Micro 节点

Mini/Micro 是通用的 CAN 节点。这些设备的功能取决于其运行的固件。
默认且最常见的用途是作为 PWM-CAN 适配器，通过 CAN（Cyphal 或 DroneCAN）通信控制电调和舵机：

- Micro 是最小的节点，具有 2 组引脚（PWM/5V/GND），可控制 2 个舵机或电调。
- Mini v2：具有 2 组 PWM/5V/GND 用于控制舵机或电调，以及 2 组 PWM/FB/GND 用于通过 UART 控制和接收电调反馈。

## Cyphal/DroneCAN CAN-PWM 适配器固件

_Cyphal/DroneCAN CAN-PWM_ 固件是 Mini 和 Micro 节点的默认固件。
这些固件将典型的 PX4 CAN 设定值转换为 PWM 信号，用于控制电调或舵机。

请参考对应的 RaccoonLab 文档页面获取详情：[Cyphal/CAN-PWM](https://docs.raccoonlab.co/guide/can_pwm/cyphal.html)，[DroneCAN-PWM](https://docs.raccoonlab.co/guide/can_pwm/dronecan.html)。

![Mini v2 节点与舵机和电调](../../assets/hardware/can_nodes/raccoonlab_mini_v2_with_servo.png)

## Cyphal & DroneCAN 距离传感器固件

_Cyphal & DroneCAN Rangefinder_ 固件是可在 Cyphal 或 DroneCAN 模式下运行的单一固件。
它支持 LW20/I2C、Garmin Lite v3/I2C 和 TL-Luna/UART 激光雷达。
详情请查看 [Rangefinder](https://docs.raccoonlab.co/guide/can_pwm/rangefinder.html) 页面。

![Mini v2 节点与舵机和电调](../../assets/hardware/can_nodes/raccoonlab_mini_v2_lw20_i2c.png)

## DroneCAN 燃料传感器固件

_DroneCAN fuel tank_ 固件基于 [AS5600 传感器板](https://docs.raccoonlab.co/guide/as5600/)。
详情请参考 [DroneCAN Fuel Tank](https://docs.raccoonlab.co/guide/can_pwm/fuel_tank.html)。

## DroneCAN 舵机夹具固件

_DroneCAN servo gripper_ 是 [Mini Node 模板应用](https://github.com/RaccoonlabDev/mini_v2_node) 的一部分。
详情请参考 [DroneCAN Servo Gripper](https://docs.raccoonlab.co/guide/can_pwm/servo_gripper.html)。

## 自定义固件

**自定义固件** 可由任何人开发。
如需定制功能，可使用 [Mini Node 模板应用](https://github.com/RaccoonlabDev/mini_v2_node)。
您可配置外部引脚以 UART、I2C 或 ADC 模式工作。
开箱即用支持基础 Cyphal/DroneCAN 功能。
包含发布者和订阅者作为示例。

## 选择哪个节点？

Mini v2 与 Micro 的区别总结如下表。
更多详情请参考对应页面。

|     |                 | Mini v2                                   | Micro                                |
| --- | --------------- | ----------------------------------------- | ------------------------------------ |
|     | 图片            | ![RaccoonLab Mini v2 节点][Mini v2 Node]  | ![RaccoonLab Micro 节点][Micro Node] |
| 1   | 输入电压        | 5.5V – 30V                                | 4.5V – 5.5V                          |
| 2   | DC-DC转换器     | 支持                                      | 不支持                               |
| 3   | 引脚组          | - PWM+5V+GND x2 </br> - PWM+FB+GND x2     | - PWM+5V+GND x2                      |
| 4   | CAN 接口        | - UCANPHY Micro x2 </br> - 6-pin Molex x2 | - UCANPHY Micro x2                   |
| 5   | SWD 接口        | +                                         | +                                    |
| 6   | 尺寸，LxWxH，mm | 42x35x12                                  | 20x10x5                              |
| 7   | 重量，g         | 5                                         | 3                                    |

[Mini v2 Node]: ../../assets/hardware/can_nodes/raccoonlab_mini_node.png
[Micro Node]: ../../assets/hardware/can_nodes/raccoonlab_micro_node.png

## 购买渠道

[RaccoonLab 商店](https://raccoonlab.co/store)

[Cyphal 商店](https://cyphal.store/search?q=raccoonlab)