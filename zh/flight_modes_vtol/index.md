# 飞行模式（VTOL）

飞行模式通过自动驾驶仪支持，使手动飞行机体更加简便，可自动化常见任务（如起飞和着陆），执行自主任务，或将飞行控制权交由外部系统。

VTOL机体可作为多旋翼或固定翼机体飞行，通常会表现出与对应机体类型完全一致的行为：

VTOL特定飞行模式行为详见：

- [着陆模式](../flight_modes_vtol/land.md): VTOL以固定翼机体飞行时，会在着陆前切换为MC模式。
- [任务模式](../flight_modes_vtol/mission.md): 任务模式支持VTOL专用任务指令，用于起飞、着陆及切换机体类型。
- [返航模式](../flight_modes_vtol/return.md): VTOL机体作为MC或FW返航时，其行为与对应机体略有差异。

其他飞行模式请参见特定机体行为：

- [飞行模式（多旋翼）](../flight_modes_mc/index.md)
- [飞行模式（固定翼）](../flight_modes_fw/index.md)

## 更多信息

- [基础配置 > 飞行模式](../config/flight_mode.md) - 如何将遥控器控制开关映射到特定飞行模式