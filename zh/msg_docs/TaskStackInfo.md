# TaskStackInfo (UORB消息)

单个运行进程的堆栈信息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/TaskStackInfo.msg)

```c
# stack information for a single running process

uint64 timestamp		# time since system start (microseconds)

uint16 stack_free
char[24] task_name

uint8 ORB_QUEUE_LENGTH = 2

```