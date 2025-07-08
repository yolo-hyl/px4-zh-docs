# IridiumsbdStatus (UORB消息)

[源文件](https://github.com/PX4/PX4-Autopilot/blob/main/msg/IridiumsbdStatus.msg)

```c
uint64 timestamp				# 系统启动后经过的时间（微秒）
uint64 last_at_ok_timestamp			# 上次收到"AT"命令后接收到"OK"的时间戳
uint16 tx_buf_write_index			# 当前发送缓冲区的大小
uint16 rx_buf_read_index			# 接收缓冲区已解析到该索引
uint16 rx_buf_end_index				# 当前接收缓冲区的大小
uint16 failed_sbd_sessions			# 失败的SBD会话数量
uint16 successful_sbd_sessions      # 成功的SBD会话数量
uint16 num_tx_buf_reset             # 发送缓冲区重置次数
uint8 signal_quality                # 当前信号质量，0表示无信号，5为最佳
uint8 state                         # 当前驱动状态，参考IridiumSBD.h中的satcom_state定义
bool ring_pending                   # 表示是否有等待中的来电
bool tx_buf_write_pending           # 表示是否有等待中的发送缓冲区写入
bool tx_session_pending             # 表示是否有等待中的发送会话
bool rx_read_pending                # 表示是否有等待中的接收读取
bool rx_session_pending             # 表示是否有等待中的接收会话
```