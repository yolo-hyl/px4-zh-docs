# 自动模式

在自动模式下，自动驾驶仪将接管机体控制以执行任务、返回发射点或执行其他自主导航任务。

要使用自动模式，必须完成[Rover Configuration/Tuning](../config_rover/index.md)中列出的所有配置/调校步骤（从[Basic Setup](../config_rover/basic_setup.md)到[Position tuning](../config_rover/position_tuning.md)）。

## 任务模式

_任务模式_ 是一种自动模式，使机体执行已上传至飞控的预定义自主[任务计划](../flying/missions.md)。  
任务通常通过地面控制站（GCS）应用程序创建并上传，例如 [QGroundControl](https://docs.qgroundcontrol.com/master/en/)。

### 任务指令

当前可用的指令（基于PX4 v1.16）如下：

| QGC任务项        | 指令                                                      | 描述                                       |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------ |
| 任务开始         | [MAV_CMD_MISSION_START](MAV_CMD_MISSION_START)               | 开始任务。                                 |
| 路点             | [MAV_CMD_NAV_WAYPOINT](MAV_CMD_NAV_WAYPOINT)                 | 导航至路点。                               |
| 返回发射点       | [MAV_CMD_NAV_RETURN_TO_LAUNCH][MAV_CMD_NAV_RETURN_TO_LAUNCH] | 返回发射位置。                             |
| 改变速度         | [MAV_CMD_DO_CHANGE_SPEED][MAV_CMD_DO_CHANGE_SPEED]           | 更改速度设定值                             |
| 设置发射位置     | [MAV_CMD_DO_SET_HOME](MAV_CMD_DO_SET_HOME)                   | 将发射位置更改为指定坐标。                 |
| 跳转至指定项（全部） | [MAV_CMD_DO_JUMP][MAV_CMD_DO_JUMP] (and other jump commands) | 跳转至指定任务项。                         |

[MAV_CMD_MISSION_START]: https://mavlink.io/en/messages/common.html#MAV_CMD_MISSION_START
[MAV_CMD_NAV_WAYPOINT]: https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_WAYPOINT
[MAV_CMD_NAV_RETURN_TO_LAUNCH]: https://mavlink.io/en/messages/common.html#MAV_CMD_NAV_RETURN_TO_LAUNCH
[MAV_CMD_DO_CHANGE_SPEED]: https://mavlink.io/en/messages/common.html#MAV_CMD_DO_CHANGE_SPEED
[MAV_CMD_DO_SET_HOME]: https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_HOME
[MAV_CMD_DO_JUMP]: https://mavlink.io/en/messages/common.html#MAV_CMD_DO_JUMP

## 返回模式

机体将返回发射位置。  
返回模式可通过对应的[任务指令](#任务指令)或通过地面站UI激活。