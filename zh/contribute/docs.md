# 贡献文档

欢迎对PX4用户指南的贡献；从简单的拼写和语法修正，到创建全新的章节。

本主题介绍如何进行修改和测试。在文末提供了基本的样式指南。

:::tip 注意
您需要拥有一个（免费的）[GitHub](https://github.com/) 账户才能向指南做出贡献。
:::

## 在 GitHub 上快速修改

对 _现有内容_ 的简单修改，可以通过点击每页底部的 **Edit on GitHub** 链接（此操作会在 Github 上打开页面以进行编辑）。

![Vitepress: Edit Page button](../../assets/vuepress/vuepress_edit_page_on_github_link.png)

编辑现有英文页面的步骤：

1. 打开页面。
1. 在页面内容下方点击 **Edit on GitHub** 链接。
1. 进行所需修改。
1. 在 Github 页面编辑器下方，您将收到提示创建一个独立分支，并引导您提交 _pull request_。

文档团队将审核该请求，决定是否合并，或与您协作进行更新。

请注意，您只能直接在源文件中修改英文版本的内容。
[翻译通过 Crowdin 处理](../contribute/translation.md)。

## 使用 Git 进行更改

更复杂的更改（例如添加新页面或添加/修改图片）在 GitHub 上不易进行（或正确测试）。

对于此类更改，建议采用与 _代码_ 相同的方法：

1. 使用 _git_ 工具链将 PX4 源代码下载到本地计算机。
1. 根据需要修改文档（添加、修改、删除）。
1. 使用 Vitepress _测试_ 构建是否正常。
1. 为更改创建一个分支，并创建拉取请求（PR）将其合并到 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot.git) 仓库中。

以下内容将说明如何获取源代码、本地构建（用于测试）以及修改代码。

### 获取文档源代码

文档源代码位于 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot/) 仓库中，与所有其他 PX4 源代码一起存放。  
源代码是 Markdown 文件，位于 [/docs](https://github.com/PX4/PX4-Autopilot/tree/main/docs) 子目录中。  
英文源文件位于 [/docs/en/](https://github.com/PX4/PX4-Autopilot/tree/main/docs/en) 子目录中，可直接编辑。  
[翻译](../contribute/translation.md) 源文件位于语言特定子目录中，例如 `ko` 表示韩语，`zh` 表示中文：这些应通过 Crowdin 工具编辑，不应直接编辑。

::: tip
如果你已经克隆了 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot/) 仓库，可以跳过本节。
:::

要将库源代码下载到本地计算机，需要使用 git 工具链。  
以下说明将解释如何获取 git 并在本地计算机上使用它。

1. 从 [https://git-scm.com/downloads](https://git-scm.com/downloads) 下载适用于你计算机的 git。
1. 如果尚未注册，请在 [https://github.com/join](https://github.com/join) 注册 GitHub 账号。
1. 在 GitHub 上创建 [PX4-Autopilot repo](https://github.com/PX4/PX4-Autopilot) 的副本（Fork）（[具体步骤](https://docs.github.com/en/get-started/quickstart/fork-a-repo)）。
1. 将你的 Fork 仓库克隆（复制）到本地计算机：

   ```sh
   cd ~/wherever/
   git clone https://github.com/<your git name>/PX4-Autopilot.git
   ```

   例如，为 GitHub 账号为 "john_citizen" 的用户克隆 PX4 源代码副本：

   ```sh
   git clone https://github.com/john_citizen/PX4-Autopilot.git
   ```

1. 导航到本地仓库：

   ```sh
   cd ~/wherever/PX4-Autopilot
   ```

1. 添加一个名为 "upstream" 的远程仓库，指向 "官方" PX4 版本的库：

   ```sh
   git remote add upstream https://github.com/PX4/PX4-Autopilot.git
   ```

   :::tip
   "远程" 是对特定仓库的引用。  
   克隆仓库时默认创建名为 _origin_ 的远程仓库，指向你 Fork 的指南。  
   以上步骤创建了一个新的远程仓库 _upstream_，指向 PX4 项目的文档版本。
   :::

### 制作/推送文档更改

在你创建的仓库中：

1. 更新你的仓库 `main` 分支到最新版本：

   ```sh
   git checkout main
   git fetch upstream main
   git pull upstream main
   ```

1. 根据需要修改文档内容。
1. 使用 Vitepress 测试构建是否正常。
1. 为更改创建一个分支，并创建拉取请求（PR）将其合并到 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot.git) 仓库中。

### 添加新页面

添加新页面时，必须同时将其添加到 `en/SUMMARY.md`！## 风格指南

1. 文件/文件名

   - 将新markdown文件放入`/en/`的合适子文件夹中，例如`/en/contribute/`
     不要进行更深层的文件夹嵌套
   - 将新图像文件放入`/assets/`的合适嵌套子文件夹中
     更深层的嵌套是允许且推荐的
   - 文件夹和文件名应具有描述性
     特别是图像文件名应描述其内容（不要命名为"image1.png"）
   - 文件名使用小写，使用下划线`_`分隔单词

2. 图像

   - 使用最小尺寸和最低分辨率仍能保持图像可用性（这能减少带宽较差用户的下载成本）
   - 新图像应创建在`/assets/`的子文件夹中（以便在翻译间共享）
   - 优先使用SVG文件进行图表绘制
     优先使用PNG文件而非JPG文件进行截图

3. 内容：

   - 一致且适度地使用"样式"（**加粗**、_斜体_等）（尽可能少用）
     - **加粗**用于按钮点击和菜单定义
     - _斜体_用于工具名称，如_QGroundControl_或_prettier_
     - `代码`用于文件路径、代码、未链接的参数名，以及命令行工具，如`prettier`
   - 标题和页面标题应使用"首字母大写"
   - 页面标题应使用一级标题`#`
     所有其他标题应使用二级标题`##`或更低级别
   - 不要对标题添加任何样式
   - 不要翻译`info`、`tip`或`warning`声明的文本（如`::: tip`），因为需要精确文本以正确渲染侧边栏
   - 优先按句子换行
     不要基于任意行长度限制进行换行
   - 使用_prettier_格式化（_VSCode_具有可为此目的使用的扩展）

4. 视频：

   - 可通过格式`<lite-youtube videoid="<youtube-video-id>" title="your title"/>`添加YouTube视频（通过[https://www.npmjs.com/package/lite-youtube-embed](https://www.npmjs.com/package/lite-youtube-embed)自定义元素支持，该元素有其他可传递参数）
     - 教程视频应谨慎使用，因为它们容易过时且难以维护
     - 机体飞行的精彩视频总是受欢迎的## 我在哪里添加修改？

将新文件添加到涵盖类似主题的文件夹中。  
然后在侧边栏（`/en/SUMMARY.md`）中按照现有结构引用它们！## 翻译

有关翻译的信息请参见：[Translation](../contribute/translation.md)

## 许可

所有 PX4/Dronecode 文档均可根据 [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) 许可条款自由使用和修改。