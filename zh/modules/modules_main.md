# 模块与命令参考

以下页面介绍了PX4模块、驱动程序和命令。  
它们描述了所提供的功能、高级实现概述以及如何使用命令行界面。

::: info
**这是从源代码自动生成的**，包含最新的模块文档。
:::

这并非完整的列表，NuttX还提供了一些额外的命令（如 `free`）。在控制台中使用 `help` 命令可以获取所有可用命令列表，大多数情况下使用 `command help` 可以打印使用说明。

由于文档是直接从源代码生成的，因此错误必须在 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot) 仓库中报告/修复。  
可以通过在PX4-Autopilot目录的根目录运行以下命令生成文档页面：

```
make module_documentation
```
生成的文件将写入 `modules` 目录。

## 分类
- [自适应调参](modules_autotune.md)
- [命令](modules_command.md)
- [通信](modules_communication.md)
- [控制器](modules_controller.md)
- [驱动程序](modules_driver.md)
- [估计器](modules_estimator.md)
- [仿真](modules_simulation.md)
- [系统](modules_system.md)
- [模板](modules_template.md)