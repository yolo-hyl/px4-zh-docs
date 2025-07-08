# ControlAllocatorStatus（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/ControlAllocatorStatus.msg)

```c
uint64 timestamp                        # 系统启动后经过的时间（微秒）

bool torque_setpoint_achieved           # 表示3D扭矩目标值是否成功分配给执行器。未达成时为0，达成时为1。
float32[3] unallocated_torque           # 未分配的扭矩。目标值达成时等于0。
                                        # 计算方式：unallocated_torque = torque_setpoint - allocated_torque

bool thrust_setpoint_achieved           # 表示3D推力目标值是否成功分配给执行器。未达成时为0，达成时为1。
float32[3] unallocated_thrust           # 未分配的推力。目标值达成时等于0。
                                        # 计算方式：unallocated_thrust = thrust_setpoint - allocated_thrust

int8 ACTUATOR_SATURATION_OK        =  0 # 执行器未饱和
int8 ACTUATOR_SATURATION_UPPER_DYN =  1 # 执行器饱和（值<=目标值）因为无法更快增加
int8 ACTUATOR_SATURATION_UPPER     =  2 # 执行器饱和（值<=目标值）因为已达到最大值
int8 ACTUATOR_SATURATION_LOWER_DYN = -1 # 执行器饱和（值>=目标值）因为无法更快减少
int8 ACTUATOR_SATURATION_LOWER     = -2 # 执行器饱和（值>=目标值）因为已达到最小值

int8[16] actuator_saturation            # 表示执行器饱和状态。
                                        # 注意1：执行器饱和不一定意味着推力目标或扭矩目标未达成。
                                        # 注意2：具有动态限制的执行器可能在未达到最大值时仍被标记为上界饱和。

uint16 handled_motor_failure_mask        # 已从分配/有效性矩阵中移除的故障电机位掩码。不一定与FailureDetector的报告一致。
```