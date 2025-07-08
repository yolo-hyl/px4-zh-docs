# 模块参考：模板

## module

源码: [templates/template_module](https://github.com/PX4/PX4-Autopilot/tree/main/src/templates/template_module)

### 描述
描述模块提供的功能部分。

这是一个作为后台任务运行的模块模板，包含启动/停止/状态功能。

### 实现
描述模块的高层级实现方式。

### 示例
CLI使用示例：
```
module start -f -p 42
```

### 用法 {#module_usage}

```
module <command> [arguments...]
 命令:
   start
     [-f]        可选示例标志位
     [-p <val>]  可选示例参数
                 默认值: 0

   stop

   status        打印状态信息
```

## work_item_example

源码: [examples/work_item](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/work_item)

### 描述
从工作队列运行的简单模块示例。

### 用法 {#work_item_example_usage}

```
work_item_example <command> [arguments...]
 命令:
   start

   stop

   status        打印状态信息
```