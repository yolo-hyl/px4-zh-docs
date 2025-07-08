# 调试数组（UORB 消息）


[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DebugArray.msg)

```c
uint8 ARRAY_SIZE = 58
uint64 timestamp            # 系统启动后经过的时间（微秒）
uint16 id                   # 调试数组的唯一 ID，用于区分不同的数组
char[10] name               # 调试数组的名称（最多10个字符）
float32[58] data            # 数据

```