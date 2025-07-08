# FuelTankStatus (UORB消息)

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/FuelTankStatus.msg)

```c
uint64 timestamp                        # 系统启动后经过的时间(微秒)

float32 maximum_fuel_capacity       	# 最大燃料容量。必须始终提供，来自驱动器或参数
float32 consumed_fuel       		# 已消耗燃料，未测量时为NaN。不应根据最大燃料容量推断
float32 fuel_consumption_rate     	# 燃料消耗率，未测量时为NaN

uint8 percent_remaining                 # 剩余燃料百分比，未提供时为UINT8_MAX
float32 remaining_fuel      		# 剩余燃料，未测量时为NaN。不应根据最大燃料容量推断

uint8 fuel_tank_id                      # 燃料罐标识符。必须与其他同一燃料系统的消息ID匹配。当只有一个油箱时默认为0

uint32 fuel_type                        # 基于MAV_FUEL_TYPE枚举的燃料类型。未知或不符合提供类型时设置为MAV_FUEL_TYPE_UNKNOWN
uint8 MAV_FUEL_TYPE_UNKNOWN = 0		# 未指定燃料类型。燃料量已归一化（即最大值为1，其他级别相对于1）。
uint8 MAV_FUEL_TYPE_LIQUID = 1		# 表示通用液体燃料，如汽油或柴油。燃料量以毫升(ml)为单位，流量以毫升/秒(ml/s)为单位。
uint8 MAV_FUEL_TYPE_GAS = 2		# 表示气体燃料，如氢气、甲烷或丙烷。燃料量以千帕(kPa)为单位，流量以毫升/秒(ml/s)为单位。

float32 temperature                     # 燃料温度，单位为开尔文，未测量时为NaN
```