# RegisterExtComponentReply（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/RegisterExtComponentReply.msg)

```c
uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动后经过的时间（微秒）

uint64 request_id          # 从请求中获取的ID
char[25] name              # 从请求中获取的名称

uint16 px4_ros2_api_version

bool success
int8 arming_check_id      # 武装检查注册ID（-1表示无效）
int8 mode_id              # 分配的模式ID（-1表示无效）
int8 mode_executor_id     # 分配的模式执行器ID（-1表示无效）

uint8 ORB_QUEUE_LENGTH = 2

```