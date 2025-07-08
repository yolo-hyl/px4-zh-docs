# 使用监听器命令进行传感器/主题调试

uORB 是一个用于线程间/进程间通信的异步 `publish()` / `subscribe()` 消息API。`listener` 命令可从 _QGroundControl MAVLink 控制台_ 使用，用于检查主题（消息）值，包括传感器当前发布的值。

:::tip
这是一个强大的调试工具，因为它即使在 QGC 通过无线链路连接时（例如当机体正在飞行时）也能使用。
:::

::: info
`listener` 命令也可通过 [System Console](../debug/system_console.md) 和 [MAVLink Shell](../debug/mavlink_shell.md) 使用。
:::

:::tip
要检查可用的主题及其速率，只需使用 `uorb top` 命令。
:::

下图演示了使用 _QGroundControl_ 获取加速度计传感器值的过程。

![QGC MAVLink 控制台](../../assets/gcs/qgc_mavlink_console_listener_command.png)

欲了解更多关于如何确定可用主题以及如何调用 `listener` 的信息，请参见：[uORB 消息传递 > 列出主题和监听](../middleware/uorb.md#listing-topics-and-listening-in)。