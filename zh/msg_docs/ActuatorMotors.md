# 执行器电机（UORB 消息）

电机控制消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ActuatorMotors.msg)

```c
# 电机控制消息

uint32 MESSAGE_VERSION = 0

uint64 timestamp			# 系统启动后时间（微秒）
uint64 timestamp_sample	    # 该控制响应所依据数据的采样时间戳

uint16 reversible_flags     # 位掩码，表示哪些电机被配置为可逆

uint8 ACTUATOR_FUNCTION_MOTOR1 = 101

uint8 NUM_CONTROLS = 12
float32[12] control # 范围：[-1, 1]，其中 1 表示最大正向推力，
                    # -1 表示最大反向推力（若输出不支持负值则映射为 NaN），
                    # NaN 表示停机（停止电机）

```