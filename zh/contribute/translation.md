# 翻译

我们欢迎您帮助翻译 _QGroundControl_、PX4 元数据（在 QGC 中）以及我们的 PX4、_QGroundControl_ 和 MAVLink 指南！

我们的文档（和 _QGroundControl_）使用 [Crowdin](https://crowdin.com) 在线工具进行翻译。Crowdin 会自动从 GitHub 导入源主题，并将新字符串和修改后的字符串呈现出来进行翻译和/或审核（批准）。

Crowdin 会通过 "Pull Request" 形式将翻译后的文档导出回 GitHub（开发团队会定期审核并接受）。导出的输出包含源文档，任何已翻译和批准的文本都会被替换为翻译后的字符串（即如果字符串未翻译/已更改，则会显示为英文）。

:::tip
您需要拥有一个（免费的）[Crowdin 账号](https://crowdin.com/join) 才能加入翻译团队！
:::

::: info
该系统的优点在于翻译能紧密跟踪源文档。读者不会因过时的翻译而被误导。
:::

## 开始使用

加入我们翻译团队的步骤如下：

1. 注册 Crowdin: [https://crowdin.com/join](https://crowdin.com/join)
1. 打开您要加入的翻译项目：
   - [QGroundControl](https://crowdin.com/project/qgroundcontrol) — QGroundControl 界面和硬编码字符串
   - [PX4-Metadata-Translations](https://crowdin.com/project/px4-metadata-translations) — QGroundControl 中的 PX4 参数和事件描述
   - [PX4 User Guide](https://crowdin.com/project/px4-user-guide)
   - [QGroundControl Developer Guide](https://crowdin.com/project/qgroundcontrol-developer-guide)
   - [QGroundControl User Guide](https://crowdin.com/project/qgroundcontrol-user-guide)
   - [MAVLink Guide](https://crowdin.com/project/mavlink)
1. 选择您要翻译的语言
1. 点击 **Join** 按钮（在 "You must join the translators team to be able to participate in this project" 文本旁边）

   ::: info
   您的加入申请被接受后会收到通知。
   :::

1. 开始翻译！

## 特别注意事项

### 不要修改前缀文本

Vuepress 使用 `:::` 标记注释、提示和警告的开始：

```html
:::tip 提示内容。:::
```

`:::tip` 或 `:::warning` 等前缀文本不应被修改，因为它们定义了注释框的颜色。

## 添加新语言

如果您要翻译的语言不可用，则需要联系项目所有者请求添加（每个项目的主页都有联系链接）。

:::warning
维护翻译非常困难！
在请求我们创建新语言之前，请先找到几位其他人来协助翻译！
:::

## 获取帮助

_Crowdin_ 界面是自解释的，但您可以在 [知识库](https://support.crowdin.com/) 获取更多信息。

您也可以通过 [我们的支持渠道](../contribute/support.md) 在 Dronecode 社区向翻译者和开发者寻求帮助。