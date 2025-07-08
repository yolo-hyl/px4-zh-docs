# 完整应用程序的模块模板

应用程序可以编写为作为 *任务*（带有自己堆栈和进程优先级的模块）运行，或作为 *工作队列任务*（在工作队列线程上运行的模块，与其他任务共享堆栈和线程优先级）运行。  
在大多数情况下可以使用工作队列任务，因为这可以最小化资源使用。

::: info  
[架构概述 > 运行时环境](../concept/architecture.md#runtime-environment) 提供了关于任务和工作队列任务的更多信息。  
:::  

::: info  
[第一个应用程序教程](../modules/hello_sky.md) 中学到的所有内容都适用于编写完整应用程序。  
:::  

## 工作队列任务  

PX4-Autopilot 包含一个用于编写作为 *工作队列任务* 运行的新应用程序（模块）的模板：  
[src/examples/work_item](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/work_item)。  

工作队列任务应用程序与普通（任务）应用程序相同，只是需要指定它是工作队列任务，并在初始化时安排自己运行。  

示例展示了如何操作。简要总结如下：  
1. 在 cmake 定义文件 ([CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/examples/work_item/CMakeLists.txt)) 中指定对工作队列库的依赖：  
   ```
   ...
   DEPENDS
      px4_work_queue
   ```
1. 除了 `ModuleBase`，任务还应派生自 `ScheduledWorkItem`（包含在 [ScheduledWorkItem.hpp]( https://github.com/PX4/PX4-Autopilot/blob/main/platforms/common/include/px4_platform_common/px4_work_queue/ScheduledWorkItem.hpp)）  
1. 在构造函数初始化中指定要添加任务的队列。  
   [work_item](https://github.com/PX4/PX4-Autopilot/blob/main/src/examples/work_item/WorkItemExample.cpp#L42) 示例将其自身添加到 `wq_configurations::test1` 工作队列中，如下所示：  
   ```cpp
   WorkItemExample::WorkItemExample() :
	   ModuleParams(nullptr),
	   ScheduledWorkItem(MODULE_NAME, px4::wq_configurations::test1)
   {
   }
   ```

   ::: info  
   可用的工作队列（`wq_configurations`）在 [WorkQueueManager.hpp](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/common/include/px4_platform_common/px4_work_queue/WorkQueueManager.hpp#L49) 中列出。  
   :::

1. 实现 `ScheduledWorkItem::Run()` 方法以执行“工作”。  
1. 实现 `task_spawn` 方法，指定该任务是工作队列任务（使用 `task_id_is_work_queue` id）。  
1. 使用调度方法之一调度工作队列任务（在示例中我们从 `init` 方法内部使用 `ScheduleOnInterval`）。  

## 任务  

PX4/PX4-Autopilot 包含一个用于编写在自己的堆栈上运行的任务的新应用程序（模块）模板：  
[src/templates/template_module](https://github.com/PX4/PX4-Autopilot/tree/main/src/templates/template_module)。  

该模板演示了完整应用程序所需或有用的附加功能/方面：  

- 访问参数并响应参数更新。  
- uORB 订阅和等待主题更新。  
- 通过 `start`/`stop`/`status` 控制后台运行的任务。  
  然后可以直接将 `module start [<arguments>]` 命令添加到  
  [启动脚本](../concept/system_startup.md)。  
- 命令行参数解析。  
- 文档：`PRINT_MODULE_*` 方法有两个用途（API 在  
  [源代码](https://github.com/PX4/PX4-Autopilot/blob/v1.8.0/src/platforms/px4_module.h#L381) 中有文档说明）：  
  - 它们用于在控制台输入 `module help` 时打印命令行用法。  
  - 它们通过脚本自动提取以生成 [模块与命令参考](../modules/modules_main.md) 页面。