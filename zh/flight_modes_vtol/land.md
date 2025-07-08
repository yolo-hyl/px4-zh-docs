# 降落模式（VTOL）

<img src="../../assets/site/position_fixed.svg" title="Position estimate required (e.g. GPS)" width="30px" />

_降落_飞行模式会使机体在该模式被启用的位置着陆。  
着陆完成后，机体将在短暂停留后自动解除武装（默认行为）。

当VTOL处于FW模式时，其降落模式行为和参数遵循[固定翼](../flight_modes_fw/land.md)；当处于MC模式时，遵循[多旋翼](../flight_modes_mc/land.md)。

默认情况下，处于FW模式的VTOL在降落前会切换回MC模式。

## 参数

VTOL专用参数包括：

| 参数名称                                                                 | 描述                                                         |
| ------------------------------------------------------------------------ | ------------------------------------------------------------ |
| [NAV_FORCE_VT](../advanced_config/parameter_reference.md#NAV_FORCE_VT) | 强制VTOL以多旋翼模式起降（默认：true）                     |

## 参见

- [降落模式（MC）](../flight_modes_mc/land.md)
- [降落模式（FW）](../flight_modes_fw/land.md)