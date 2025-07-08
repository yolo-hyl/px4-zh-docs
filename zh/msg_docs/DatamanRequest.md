# DatamanRequest (UORB message)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DatamanRequest.msg)

```c
uint64 timestamp	# 系统启动后经过的时间（微秒）

uint8 client_id
uint8 request_type	# id/读取/写入/清除
uint8 item			# dm_item_t
uint32 index
uint8[56] data
uint32 data_length

```