# DatamanResponse（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/DatamanResponse.msg)

```c
uint64 timestamp	# 系统启动后时间（微秒）

uint8 client_id
uint8 request_type	# id/读取/写入/清除
uint8 item			# dm_item_t
uint32 index
uint8[56] data

uint8 STATUS_SUCCESS = 0
uint8 STATUS_FAILURE_ID_ERR = 1
uint8 STATUS_FAILURE_NO_DATA = 2
uint8 STATUS_FAILURE_READ_FAILED = 3
uint8 STATUS_FAILURE_WRITE_FAILED = 4
uint8 STATUS_FAILURE_CLEAR_FAILED = 5
uint8 status

```