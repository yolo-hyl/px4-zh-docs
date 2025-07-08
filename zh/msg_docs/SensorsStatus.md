# 传感器状态 (UORB 消息)

传感器检查指标。对于主传感器或未安装的传感器，该值为零。

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorsStatus.msg)

```c
#
# 传感器检查指标。对于主传感器或未安装的传感器，该值为零。
#
uint64 timestamp # 系统启动以来的时间（微秒）

uint32 device_id_primary       # 当前主设备ID，供参考

uint32[4] device_ids
float32[4] inconsistency       # 传感器实例与平均值之间的差异幅度
bool[4] healthy                # 传感器状态是否正常
uint8[4] priority
bool[4] enabled
bool[4] external

# TOPICS sensors_status_baro sensors_status_mag

```