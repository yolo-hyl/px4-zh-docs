# RegisterExtComponentRequest (UORB消息)

注册外部组件的请求

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/versioned/RegisterExtComponentRequest.msg)

```c
# 注册外部组件的请求

uint32 MESSAGE_VERSION = 0

uint64 timestamp # 系统启动以来的时间（微秒）

uint64 request_id                  # ID，设置为随机值
char[25] name                      # 请求的模式名称或组件名称

uint16 LATEST_PX4_ROS2_API_VERSION = 1 # API版本兼容性。当语义发生破坏性变更时增加该值。任何消息字段的变更会单独检测，不需要API版本变更。

uint16 px4_ros2_api_version   # 设置为LATEST_PX4_ROS2_API_VERSION

# 要注册的组件
bool register_arming_check
bool register_mode                 # 注册模式时需要同时设置上锁检查
bool register_mode_executor        # 注册执行器时需要同时注册模式（该模式由执行器拥有）

bool enable_replace_internal_mode  # 如果应替换内部模式则设置为true
uint8 replace_internal_mode        # 机体状态::NAVIGATION_STATE_*
bool activate_mode_immediately     # 切换到已注册模式（只能与执行器组合使用）


uint8 ORB_QUEUE_LENGTH = 2

```