# PWM舵机夹爪

本节说明如何连接和配置使用飞控PWM输出控制的[夹爪](../peripherals/gripper.md)（舵机执行器）。

![R4-EM-R22-16: 高负载夹爪示例](../../assets/hardware/grippers/highload_gripper_example.jpg)

## 支持的夹爪

以下通过PWM连接的舵机已通过PX4测试：

- [R4-EM-R22-161推杆式电子锁](https://southco.com/en_any_int/r4-em-r22-161) (SouthCo)
- [FluxGrip FG40电磁永久夹爪](http://zubax.com/fg40) (Zubax)

## PWM控制夹爪的连接

PWM线缆包含三根线：电源、地线和信号线。
典型连接器如图所示：

![PWM线缆](../../assets/hardware/grippers/pwm_cable.png)

上图中线缆颜色对应关系如下：

| 线缆颜色 | 功能       |
| -------- | ---------- |
| 棕色     | 地线       |
| 红色     | 电源       |
| 黄色     | PWM信号    |

需要将其正确连接到飞控的PWM输入接口。

### 兼容性检查

连接线缆前请确认以下要求：

- **信号线电压等级**：查阅夹爪机制的数据手册确认信号线电压等级，并确保与飞控引脚电压等级兼容。
- **夹爪供电需求**：查阅机制数据手册确认供电线电压需求。根据需求，夹爪可直接连接到[电源模块](../power_module/index.md)或5V线路，也可使用自定义电压调节器输出其他所需电压。

## PX4配置

配置说明请参考：[夹爪 > PX4配置](../peripherals/gripper.md#px4-configuration) 文档。

特别注意：舵机夹爪必须按照下文映射到输出接口。

### 执行器映射

PWM舵机夹爪及其他直接连接PWM输出的外设，必须在[执行器配置](../config/actuators.md#actuator-outputs)中映射到特定输出口。

通过将`Gripper`功能分配到夹爪连接的输出端口实现。例如下图将`Gripper`分配到PWM AUX5输出口。

![夹爪输出映射](../../assets/config/gripper/qgc_gripper_output_setup.png)

还需为夹爪输出端口设置正确的PWM频率（商业舵机/夹爪通常为50Hz）。

::: info
错误配置频率可能损坏夹爪。
:::

在[执行器测试](../config/actuators.md#actuator-testing)配置界面的滑块可以验证输出是否正常。最小和最大PWM值应设置为：在解除武装时舵机完全闭合，在最大滑块位置时完全打开。