# Ping (UORB 消息)



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/Ping.msg)

```c
uint64 timestamp			# 系统启动后的时间（微秒）
uint64 ping_time			# ping数据包的时间戳
uint32 ping_sequence		# ping数据包的序号
uint32 dropped_packets		# 丢失的ping数据包数量
float32 rtt_ms				# 往返时间（单位：毫秒）
uint8 system_id				# 远端系统的系统ID
uint8 component_id			# 远端系统的组件ID

```