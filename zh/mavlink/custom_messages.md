# 自定义MAVLink消息

自定义[MAVLink消息](../middleware/mavlink.md)是指那些未包含在PX4默认集成的标准MAVLink定义中的消息。

::: info
如果使用自定义定义，您将需要分叉并维护PX4、地面站以及与之通信的任何SDK。  
通常建议尽可能使用（或添加到）标准定义，以减少维护负担。
:::

## 添加自定义XML

自定义定义可以添加到新方言文件中，位置应与[使用标准XML定义](../mavlink/adding_messages.md)时相同。  
例如，创建 `PX4-Autopilot/src/modules/mavlink/mavlink/message_definitions/v1.0/custom_messages.xml`，并将`CONFIG_MAVLINK_DIALECT`设置为在SITL中构建新文件。  
该方言文件应包含`development.xml`，以便同时包含所有标准定义。

在初步原型设计，或希望消息成为"标准"时，也可以将消息添加到`common.xml`（或`development.xml`）中。  
这简化了构建过程，因为无需修改构建的方言文件。

MAVLink开发指南解释了如何在[如何定义MAVLink消息和枚举](https://mavlink.io/en/guide/define_xml_element.html)中定义新消息。

您可以通过检查构建目录中生成的头文件（`/build/<build target>/mavlink/`）来验证新消息是否已构建。  
如果消息未被构建，可能是格式错误或使用了冲突的ID。  
检查构建日志获取详细信息。

消息构建完成后，即可按照以下章节描述的方式进行流式传输、接收或使用。

::: info
[MAVLink开发指南](https://mavlink.io/en/getting_started/)提供了关于使用MAVLink工具链的更多信息。
:::

## 自定义MAVLink消息的替代方案

有时需要自定义MAVLink消息，但其内容尚未完全定义。

例如，当使用MAVLink将PX4与嵌入式设备接口时，飞行控制器和设备之间交换的消息可能需要多次迭代才能稳定。  
在这种情况下，重新生成MAVLink头文件并确保双方使用相同协议版本会耗费大量时间且容易出错。

一种替代的临时解决方案是重用调试消息。  
例如，不需要创建自定义MAVLink消息`CA_TRAJECTORY`，而是可以发送`DEBUG_VECT`消息，使用字符串键`CA_TRAJ`，并将数据放入`x`、`y`和`z`字段。  
[本教程](../debug/debug_values.md)展示了调试消息的使用示例。

::: info
此方案效率不高，因为它在网络上传输字符字符串并涉及字符串比较。  
仅应用于开发阶段！
:::

## 测试与更新地面站

代码测试和地面站更新与[添加新标准MAVLink定义](../mavlink/adding_messages.md)的流程相同。