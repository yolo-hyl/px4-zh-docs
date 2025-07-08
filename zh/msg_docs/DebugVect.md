# DebugVect (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DebugVect.msg)

```c
uint64 timestamp	# 自系统启动以来的时间（微秒）
char[10] name           # 最多10个字符作为键/名称
float32 x               # x值
float32 y               # y值
float32 z               # z值

```