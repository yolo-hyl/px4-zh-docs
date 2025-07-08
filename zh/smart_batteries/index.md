# 智能电池

智能电池相比自动驾驶仪对"普通电池"的估算，能够提供更准确(且通常更详细)的电池状态信息。这使得飞行计划中的故障条件通知更加可靠。信息可能包含以下部分：剩余电量、空电时间(预估)、电芯电压(标称最大/最小值、当前电压等)、温度、电流、故障信息、电池厂商、化学类型等。

PX4支持(至少)以下智能电池：
* [Rotoye Batmon](../smart_batteries/rotoye_batmon.md)

### 更多信息

- [Mavlink Battery Protocol](https://mavlink.io/en/services/battery.html)
- [batt_smbus](../modules/modules_driver.md) - PX4 SMBus电池驱动文档
- [Safety > Low Battery Failsafe](../config/safety.md#battery-level-failsafe)