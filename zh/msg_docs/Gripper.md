# 抓取器（UORB消息）

# 用于命令抓取器中的执行器动作，该动作映射到控制分配模块的特定输出

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Gripper.msg)

```c
## 用于命令抓取器中的执行器动作，该动作映射到控制分配模块的特定输出

uint64 timestamp

int8 command		# 抓取器的命令状态
int8 COMMAND_GRAB = 0
int8 COMMAND_RELEASE = 1

```