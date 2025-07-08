# 执行器舵机 (UORB 消息)

舵机控制消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/ActuatorServos.msg)

```c
# Servo control message

uint32 MESSAGE_VERSION = 0

uint64 timestamp			# 系统启动后的时间（微秒）
uint64 timestamp_sample	    # 生成此控制响应所依据数据的采样时间戳

uint8 NUM_CONTROLS = 8
float32[8] control # 范围：[-1, 1]，其中 1 表示最大正向位置，
                   # -1 表示最大负向位置，
                   # 且 NaN 表示未启用状态
```