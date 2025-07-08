# 测试 MC_02 - 完全自主飞行

## 目标

测试自动模式如任务、返航等

## 预飞检查

在地面规划任务。确保任务包含

- 第一个航点为起飞点  
- 任务全程包含高度变化  
- 最后一个航点为返航指令  
- 持续时间5到6分钟  

## 飞行测试

❏ 任务  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 自动起飞  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 任务全程高度变化  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 第一个航点设置为起飞  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 启用任务结束返航  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 持续时间5到6分钟  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 着陆后自动解除武装  

❏ 任务 + 手动解锁  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 在任何手动模式下解锁  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 切换至自动模式（任务模式）触发起飞  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 观察路径跟踪、转角处理和返航性能  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 着陆后，机体应在2秒内自动解除武装（解除武装时间由参数：[COM_DISARM_LAND](../advanced_config/parameter_reference.md#COM_DISARM_LAND)设定）  

❏ 返航  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 位置模式下解锁并起飞  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 从起点飞出约10米  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 切换至返航模式  

&nbsp;&nbsp;&nbsp;&nbsp;❏ 观察路径跟踪、转角处理和返航性能  

## 预期结果  

- 油门提升时起飞应平稳  
- 任务应能在首次尝试时成功上传  
- 切换至自动模式后机体应自动起飞  
- 机体应在返航前调整至返航高度  
- 着陆后机体不应在地面弹跳  

<!--  
MC_002 - 完全自主飞行  

-	确保自动解除武装功能已启用  
-	QGC打开test1_mission.plan并同步到机体  
-	通过QGC的开始任务滑块起飞  
-	检查机体完成任务  
-	允许机体自动着陆，必要时切换手动控制并在日志描述中说明原因  
-	检查机体是否自动解除武装  
-->