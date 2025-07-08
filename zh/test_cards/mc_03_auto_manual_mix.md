# 测试 MC_03 - 自动手动混合

## 目标

测试在不同模式之间的切换

## 飞行前

- 第一个航点为起飞点
- 任务过程中高度持续变化
- 最后一个航点不是RTL，而是普通航点
- 持续时间为5到6分钟

## 飞行测试

❏ 位置+任务

&nbsp;&nbsp;&nbsp;&nbsp;❏ 以位置模式解锁并起飞

&nbsp;&nbsp;&nbsp;&nbsp;❏ 启用自动模式

&nbsp;&nbsp;&nbsp;&nbsp;❏ 观察轨迹跟踪和转向性能

&nbsp;&nbsp;&nbsp;&nbsp;❏ 任务完成后切换回位置模式

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;❏ 操纵杆居中时水平位置应保持当前值

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;❏ 操纵杆居中时垂直位置应保持当前值

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;❏ 油门响应设置为爬升/下降速率

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;❏ 俯仰/横滚/偏航响应设置为俯仰/横滚/偏航速率

&nbsp;&nbsp;&nbsp;&nbsp;❏ 启用RTL

&nbsp;&nbsp;&nbsp;&nbsp;❏ 着陆后，机体应在2秒内自动断开（断开时间由参数COM_DISARM_LAND设置）

❏ 任务+控制器中断

&nbsp;&nbsp;&nbsp;&nbsp;❏ 以位置模式解锁并起飞

&nbsp;&nbsp;&nbsp;&nbsp;❏ 启用自动模式

&nbsp;&nbsp;&nbsp;&nbsp;❏ 观察轨迹跟踪和转向性能

&nbsp;&nbsp;&nbsp;&nbsp;❏ 在到达最后一个航点前，移动操纵杆并确保机体进入位置飞行模式

&nbsp;&nbsp;&nbsp;&nbsp;❏ 手动将无人机移动到着陆区域

&nbsp;&nbsp;&nbsp;&nbsp;❏ 启用着陆模式

&nbsp;&nbsp;&nbsp;&nbsp;❏ 着陆后，机体应在2秒内自动断开（断开时间由参数COM_DISARM_LAND设置）

## 预期结果

- 油门提升时起飞应平稳
- 所有上述飞行模式中不应出现振荡
- 移动操纵杆时无人机进入位置飞行模式
- 着陆时机体不应在地面弹跳