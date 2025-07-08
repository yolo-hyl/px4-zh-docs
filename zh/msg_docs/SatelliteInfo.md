# 卫星信息（UORB 消息）  

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SatelliteInfo.msg)  

```c
uint64 timestamp		# 自系统启动以来的时间（微秒）
uint8 SAT_INFO_MAX_SATELLITES = 20

uint8 count			# 接收器可见的卫星数量
uint8[20] svid	 		# 空间载具ID [1..255]，见下方编码方案
uint8[20] used			# 0: 卫星未被使用，1: 用于导航
uint8[20] elevation		# 卫星仰角（0: 正上方，90: 地平线）
uint8[20] azimuth		# 卫星方向，0: 0度，255: 360度
uint8[20] snr			# dBHz，卫星载噪比 C/N0，范围 0..99，未追踪卫星时为0
uint8[20] prn                   # 卫星PRN码分配（伪随机数SBAS，有效代码为120-144）
```