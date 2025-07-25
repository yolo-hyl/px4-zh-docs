# 飞行模式（多旋翼）

飞行模式为飞控系统提供支持，以便更轻松地手动操控机体，自动化常见任务（如起飞和着陆），执行自主任务，或将飞行控制权转移至外部系统。

本主题概述了多旋翼和直升机可用的飞行模式。

## 概览

飞行模式分为 **手动模式** 或 **自主模式**。  
手动模式在手动飞行（使用遥控器摇杆或手柄）时提供不同程度的飞控支持，而 **自主模式** 可完全由飞控系统控制。

### 手动-易用模式

- [位置模式](../flight_modes_mc/position.md) — 具备定位/GPS的机体最易用且最安全的手动模式。  
  滚转和俯仰摇杆控制机体在前后和左右方向的**地面加速度**（类似汽车油门踏板），偏航摇杆控制水平旋转，油门控制升降速度。  
  松开摇杆后机体将保持水平姿态，主动减速停止，并锁定当前三维位置（即使存在风力和其他外力）。
- [慢速位置模式](../flight_modes_mc/position_slow.md) — 速度和偏航率受限的**位置模式**版本。  
  主要用于靠近障碍物时临时限速，或法规要求的限速场景。
- [高度模式](../flight_modes_mc/altitude.md) — 无GPS时最易用且最安全的手动模式。  
  与**位置模式**的主要区别是松开摇杆后机体将保持水平姿态和当前高度，但不会主动制动或保持水平位置（机体将随当前动量移动并受风力影响漂移）。
- [稳定模式](../flight_modes_mc/manual_stabilized.md) — 松开摇杆后机体保持水平姿态（但不维持高度和位置）。  
  机体将随动量继续移动，高度和水平位置可能受风力影响。  
  如果地面站选择"手动模式"，也会使用此模式。

### 手动-特技模式

- [特技模式](../flight_modes_mc/acro.md) — 用于执行翻滚、环飞等特技动作的手动模式。  
  松开摇杆后机体停止在滚转、俯仰、偏航轴的旋转，但不会进行其他稳定动作。

### 自主模式

- [悬停模式](../flight_modes_mc/hold.md) — 机体在当前位置和高度悬停，抵抗风力和其他外力保持位置。
- [返航模式](../flight_modes_mc/return.md) — 机体爬升至安全高度，飞行至安全位置（家点或集合点）后着陆。  
  此模式需要全局定位（GPS）。
- [任务模式](../flight_modes_mc/mission.md) — 机体执行已上传至飞控的[预定义任务/飞行计划](../flying/missions.md)。  
  此模式需要全局定位（GPS）。
- [起飞模式](../flight_modes_mc/takeoff.md) — 机体垂直起飞后切换至**悬停模式**。
- [着陆模式](../flight_modes_mc/land.md) — 机体立即着陆。
- [绕圈模式](../flight_modes_mc/orbit.md) — 机体绕圆周飞行，偏航以始终面向中心。  
  可通过遥控控制调整绕飞半径、方向、速度等。
- [跟随模式](../flight_modes_mc/follow_me.md) — 机体跟随提供定位指令的信标飞行。  
  可通过遥控设置跟随位置。
- [外部控制模式](../flight_modes_mc/offboard.md) — 机体遵循通过MAVLink或ROS 2提供的定位、速度或姿态指令。

飞行员通过遥控器上的开关或地面控制站切换飞行模式（见[飞行模式配置](../config/flight_mode.md)）。  
某些飞行模式仅在特定预飞和飞行条件满足时可用（例如GPS锁定、空速传感器、机体姿态轴感知）。  
PX4在条件满足前不会允许切换至这些模式。

选择左侧特定模式的链接以获取详细技术信息。

## 更多信息

- [基本配置 > 飞行模式](../config/flight_mode.md) - 如何将遥控开关映射到特定飞行模式
- [飞行模式（固定翼）](../flight_modes_fw/index.md)
- [飞行模式（垂直起降）](../flight_modes_vtol/index.md)
- [驾驶模式（车）](../flight_modes_rover/index.md)