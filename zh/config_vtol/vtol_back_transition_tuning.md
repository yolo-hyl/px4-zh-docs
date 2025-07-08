# VTOL 后转换调优

当 VTOL 执行后转换（从固定翼模式转换为多旋翼）时，机体需要减速以便多旋翼控制系统能有效接管。
为实现减速，当当前减速度低于设定的预期减速度参数 ([VT_B_DEC_MSS](../advanced_config/parameter_reference.md#VT_B_DEC_MSS)) 时，控制器会提升机体俯仰角。
该减速控制器的响应可通过 `I` 增益进行调节：[VT_B_DEC_I](../advanced_config/parameter_reference.md#VT_B_DEC_I)。
增加 `I` 值将导致更激进的俯仰角调整以达到配置的减速度设置。

当水平速度达到多旋翼巡航速度 ([MPC_XY_CRUISE](../advanced_config/parameter_reference.md#MPC_XY_CRUISE)) 或后转换持续时间 ([VT_B_TRANS_DUR](../advanced_config/parameter_reference.md#VT_B_TRANS_DUR)) 耗尽时（以先到者为准），机体将判定后转换完成。

## 预期减速度设置

当使用 [VTOL_LAND](https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_VTOL_LAND) 路点执行任务时，自动驾驶仪会尝试计算合适的后转换起始距离。它通过分析当前速度（相当于地面速度）和预期减速度来实现。
若要使机体在接近着陆点时完成后转换，可以调节预期减速度参数 ([VT_B_DEC_MSS](../advanced_config/parameter_reference.md#VT_B_DEC_MSS))。
请确保设置的后转换持续时间足够长，以便在超时生效前机体能够到达目标位置。

## 后转换持续时间

设置较长的后转换时间 ([VT_B_TRANS_DUR](../advanced_config/parameter_reference.md#VT_B_TRANS_DUR)) 可为机体提供更多减速时间。
在此期间，VTOL 将关闭固定翼电机并逐步提升多旋翼电机功率进行滑翔。
设置的持续时间越长，机体滑翔减速的时间越久。需要注意的是，此期间机体仅控制高度而非位置，因此可能出现一定位移偏差。