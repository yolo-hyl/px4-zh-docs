# UlogStream (UORB消息)

用于从记录器流式传输Ulog数据的消息。对应于LOGGING_DATA MAVLink消息

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/UlogStream.msg)

```c
# 用于从记录器流式传输Ulog数据的消息。对应于LOGGING_DATA
# MAVLink消息

uint64 timestamp		# 系统启动后经过的时间（微秒）

# 标志位掩码
uint8 FLAGS_NEED_ACK = 1	# 如果设置，该消息需要被确认。
				# 确认消息以同步方式发布：发布者在发送
				# 下一条消息前会等待确认

uint8 length			# 数据长度
uint8 first_message_offset	# 数据中第一条消息的起始偏移量。这
				# 用于恢复，当上一条消息丢失时
uint16 msg_sequence		# 用于判断数据丢失
uint8 flags			# 参见FLAGS_*
uint8[249] data		# ulog数据

uint8 ORB_QUEUE_LENGTH = 16	# TODO: 如果MAVLink对主题进行轮询，我们可能可以减少这个值

```