# MavlinkTunnel (UORB消息)

MAV_TUNNEL_PAYLOAD_TYPE 枚举

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/MavlinkTunnel.msg)

```c
# MAV_TUNNEL_PAYLOAD_TYPE 枚举

uint8 MAV_TUNNEL_PAYLOAD_TYPE_UNKNOWN = 0                # 未知负载类型
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED0 = 200    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED1 = 201    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED2 = 202    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED3 = 203    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED4 = 204    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED5 = 205    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED6 = 206    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED7 = 207    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED8 = 208    # 预留用于STorM32云台控制器
uint8 MAV_TUNNEL_PAYLOAD_TYPE_STORM32_RESERVED9 = 209    # 预留用于STorM32云台控制器

uint64 timestamp	     # 系统启动后时间戳(微秒)
uint16 payload_type      # 标识负载内容的代码(默认为0表示未知)。若代码小于32768，则为已注册的负载类型，对应代码应添加到MAV_TUNNEL_PAYLOAD_TYPE枚举中。软件开发者可根据需要注册类型块。大于32767的代码用于本地实验，不应提交到广泛分发的代码库中。
uint8 target_system      # 系统ID(可设为0进行广播，但不推荐)
uint8 target_component   # 组件ID(可设为0进行广播，但不推荐)
uint8 payload_length     # 负载数据长度
uint8[128] payload       # 数据本身

# 已知负载类型的主题别名
# TOPICS mavlink_tunnel esc_serial_passthru
```