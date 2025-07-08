# CellularStatus（UORB消息）

蜂窝状态

此消息当前仅用于记录MAVLink的蜂窝状态。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/CellularStatus.msg)

```c
# 蜂窝状态
#
# 此消息当前仅用于记录MAVLink的蜂窝状态。

uint64 timestamp # [us] 系统启动以来的时间。

uint16 status # [@enum STATUS_FLAG] 状态位图。
uint16 STATUS_FLAG_UNKNOWN = 1 # 状态未知或不可报告。
uint16 STATUS_FLAG_FAILED = 2 # 调制解调器不可用。
uint16 STATUS_FLAG_INITIALIZING = 4 # 调制解调器正在初始化。
uint16 STATUS_FLAG_LOCKED = 8 # 调制解调器已锁定。
uint16 STATUS_FLAG_DISABLED = 16 # 调制解调器未启用且已断电。
uint16 STATUS_FLAG_DISABLING = 32 # 调制解调器正在向STATUS_FLAG_DISABLED状态转换。
uint16 STATUS_FLAG_ENABLING = 64 # 调制解调器正在向STATUS_FLAG_ENABLED状态转换。
uint16 STATUS_FLAG_ENABLED = 128 # 调制解调器已启用并通电，但未注册网络提供商且不可用于数据连接。
uint16 STATUS_FLAG_SEARCHING = 256 # 调制解调器正在搜索网络提供商以注册。
uint16 STATUS_FLAG_REGISTERED = 512 # 调制解调器已注册网络提供商，数据连接和消息可能可用。
uint16 STATUS_FLAG_DISCONNECTING = 1024 # 调制解调器正在断开并停用最后一个活动分组数据承载。如果有多个分组数据承载处于活动状态且其中一个被停用，则不会进入此状态。
uint16 STATUS_FLAG_CONNECTING = 2048 # 调制解调器正在激活并连接第一个分组数据承载。当其他承载已处于活动状态时，后续承载的激活不会进入此状态。
uint16 STATUS_FLAG_CONNECTED = 4096 # 一个或多个分组数据承载处于活动连接状态。

uint8 failure_reason # [@enum FAILURE_REASON] 失败原因。
uint8 FAILURE_REASON_NONE = 0 # 无错误。
uint8 FAILURE_REASON_UNKNOWN = 1 # 错误状态未知。
uint8 FAILURE_REASON_SIM_MISSING = 2 # 调制解调器需要SIM卡但未插入。
uint8 FAILURE_REASON_SIM_ERROR = 3 # SIM卡可用，但无法用于连接。

uint8 type # [@enum CELLULAR_NETWORK_RADIO_TYPE] 蜂窝网络无线电类型。
uint8 CELLULAR_NETWORK_RADIO_TYPE_NONE = 0 # 无
uint8 CELLULAR_NETWORK_RADIO_TYPE_GSM = 1 # GSM
uint8 CELLULAR_NETWORK_RADIO_TYPE_CDMA = 2 # CDMA
uint8 CELLULAR_NETWORK_RADIO_TYPE_WCDMA = 3 # WCDMA
uint8 CELLULAR_NETWORK_RADIO_TYPE_LTE = 4 # LTE

uint8 quality # [dBm] 蜂窝网络接收信号强度指示/参考信号接收功率，绝对值。
uint16 mcc # [@invalid UINT16_MAX] 移动国家代码。
uint16 mnc # [@invalid UINT16_MAX] 移动网络代码。
uint16 lac # [@invalid 0] 位置区域码。
```