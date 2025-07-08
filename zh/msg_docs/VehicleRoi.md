# VehicleRoi (UORB消息)

机体兴趣区域 (ROI)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/VehicleRoi.msg)

```c
# Vehicle Region Of Interest (ROI)

uint64 timestamp			# 系统启动后经过时间(微秒)

uint8 ROI_NONE = 0			# 无兴趣区域
uint8 ROI_WPNEXT = 1			# 指向下一个任务点(可选偏移)
uint8 ROI_WPINDEX = 2			# 指向指定任务点
uint8 ROI_LOCATION = 3			# 指向固定坐标位置
uint8 ROI_TARGET = 4			# 指向目标
uint8 ROI_ENUM_END = 5

uint8 mode          # ROI模式(见上文)

float64 lat			    # 要指向的纬度
float64 lon			    # 要指向的经度
float32 alt			    # 要指向的高度

# 指向下一个航路点的附加角度偏移(仅与ROI_WPNEXT配合使用)
float32 roll_offset		# 滚转角偏移(弧度)
float32 pitch_offset		# 俯仰角偏移(弧度)
float32 yaw_offset		# 偏航角偏移(弧度)

```