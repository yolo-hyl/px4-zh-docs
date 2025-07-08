# RadioStatus（UORB消息）



[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/RadioStatus.msg)

```c

uint64 timestamp	# 系统启动后的时间（微秒）

uint8 rssi				# 本地信号强度
uint8 remote_rssi			# 远端信号强度

uint8 txbuf				# 发送缓冲区的填充百分比
uint8 noise				# 背景噪声水平

uint8 remote_noise			# 远端背景噪声水平
uint16 rxerrors				# 接收错误次数

uint16 fix				# 已纠正的错误数据包数量

```