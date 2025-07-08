# UnregisterExtComponent（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/UnregisterExtComponent.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动后的时间（微秒）

char[25] name                      # 模式名称或组件名称

int8 arming_check_id      # 上锁检查注册ID（未注册时为-1）
int8 mode_id              # 分配的模式ID（未注册时为-1）
int8 mode_executor_id     # 分配的模式执行器ID（未注册时为-1）

```