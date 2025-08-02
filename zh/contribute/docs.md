

# 参与文档编写

PX4用户指南的贡献非常受欢迎；无论是拼写和语法的简单修正，还是全新章节的创建。

本主题将解释如何进行修改和测试。
在最后部分有一个基本的样式指南。

:::tip 提示
您需要拥有一个（免费的）[GitHub](https://github.com/) 账户才能为指南做出贡献。
:::

## 在Github上快速修改

通过点击每页底部的**在GitHub上编辑**链接，可以对现有内容进行简单修改（这会打开GitHub页面进行编辑）。

![Vitepress: 编辑页面按钮](../../assets/vuepress/vuepress_edit_page_on_github_link.png)

要编辑现有英文页面：

1. 打开页面。
1. 点击页面内容下方的**在GitHub上编辑**链接。
1. 进行所需的修改。
1. 在GitHub页面编辑器下方，您将被提示创建一个独立分支，然后引导您提交**拉取请求**。

文档团队将审核请求并合并或与您合作更新内容。

请注意，您只能直接修改源文件中的英文版本。
[翻译工作通过Crowdin进行](../contribute/translation.md)

## 使用 Git 进行更改

更重大的更改（例如添加新页面或添加/修改图像）在 GitHub 上进行（或正确测试）并不容易。

对于这些类型的更改，我们建议采用与代码相同的方法：

1. 使用 _git_ 工具链将 PX4 源代码下载到本地计算机。
1. 根据需要修改文档（添加、更改、删除）。
1. 使用 Vitepress 测试其构建是否正常。
1. 为更改创建一个分支，并创建拉取请求（PR）将其合并到 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot.git) 仓库中。

以下内容解释了如何获取源代码、本地构建（用于测试）以及修改代码。

### 获取文档源代码

文档源代码位于 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot/) 仓库中，与所有其他 PX4 源代码一同存放。源文件是 Markdown 格式文件，位于 [/docs](https://github.com/PX4/PX4-Autopilot/tree/main/docs) 子目录下。英文源文件位于 [/docs/en/](https://github.com/PX4/PX4-Autopilot/tree/main/docs/en) 子目录，可直接编辑。[翻译](../contribute/translation.md) 源代码位于语言特定的子目录中，例如 `ko` 代表韩语，`zh` 代表中文：这些文件通过 Crowdin 工具编辑，不应直接修改。

::: tip
如果您已经克隆了 [PX4-Autopilot](https://github.com/PX4/PX4-Autopilot/) 仓库，可以忽略本节内容。
:::

要将库源代码获取到本地计算机，您需要使用 git 工具链。以下说明将解释如何获取 git 并在本地计算机上使用它。

1. 从 [https://git-scm.com/downloads](https://git-scm.com/downloads) 下载适用于您计算机的 git
1. 如果尚未注册，请[注册](https://github.com/join) GitHub 账号
1. 在 GitHub 上创建 [PX4-Autopilot repo](https://github.com/PX4/PX4-Autopilot) 的副本（Fork）（[操作说明](https://docs.github.com/en/get-started/quickstart/fork-a-repo)）
1. 将您 fork 的仓库克隆（复制）到本地计算机：

   ```sh
   cd ~/wherever/
   git clone https://github.com/<your git name>/PX4-Autopilot.git
   ```

   例如，克隆 PX4 源代码 fork 版本的用户（GitHub 账号为 "john_citizen"）：

   ```sh
   git clone https://github.com/john_citizen/PX4-Autopilot.git
   ```

1. 进入本地仓库：

   ```sh
   cd ~/wherever/PX4-Autopilot
   ```

1. 添加一个名为 "upstream" 的远程仓库，指向 PX4 官方版本的库：

   ```sh
   git remote add upstream https://github.com/PX4/PX4-Autopilot.git
   ```

   :::tip
   "remote" 是特定仓库的引用。克隆仓库时默认创建名为 _origin_ 的远程仓库，指向您 fork 的指南。上述操作创建了一个新的远程仓库 _upstream_，指向 PX4 项目的文档版本。
   :::

### 制作/提交文档修改

在上述创建的仓库中：

1. 更新你本地仓库的 `main` 分支至最新版本：

   ```sh
   git checkout main
   git fetch upstream main
   git pull upstream main
   ```

2. 为你的修改创建一个新分支：

   ```sh
   git checkout -b <your_feature_branch_name>
   ```

   这将在你的电脑上创建一个名为 `your_feature_branch_name` 的本地分支。

3. 根据需要修改文档（后续章节将提供一般性指导）
4. 确认修改后，可通过 "commit" 将更改添加到本地分支：

   ```sh
   git add <file name>
   git commit -m "<your commit message>"
   ```

   关于良好提交信息的撰写，请参考 [源代码管理](../contribute/code.md#commits-and-commit-messages) 章节。

5. 将本地分支（包括其中的提交记录）推送到你在Github上的fork仓库：

   ```sh
   git push origin your_feature_branch_name
   ```

6. 在网页浏览器中访问你的fork仓库（例如：`https://github.com/<your git name>/PX4-Autopilot.git`）。
   此时你应该能看到一条提示信息，显示新分支已推送到你的fork仓库。
7. 创建拉取请求（PR）：

   - 在 "new branch message" 的右侧（见上一步），你应该能看到一个绿色按钮，显示 "Compare & Create Pull Request"。
     点击该按钮。
   - 系统将创建一个拉取请求模板。
     模板会列出你的提交记录，你可以（必须）添加一个有意义的标题（如果是单提交PR，通常就是提交信息）和消息（<span style="color:orange">说明你做了什么以及原因</span>。
     可参考 [其他拉取请求](https://github.com/PX4/PX4-Autopilot/pulls) 进行对比）。
   - 添加 "Documentation" 标签。

8. 完成！

   PX4用户指南的维护者将审阅你的贡献并决定是否将其合并。
   请定期查看他们是否对你所做的更改有疑问。

### 本地构建库

本地构建库以测试您所做的任何更改是否已正确渲染：

1. 安装 [Vitepress 前置条件](https://vitepress.dev/guide/getting-started#prerequisites)：

   - [Nodejs 18+](https://nodejs.org/en)
   - [Yarn classic](https://classic.yarnpkg.com/en/docs/install)

1. 导航到本地仓库和 `/docs` 子目录：

   ```sh
   cd ~/wherever/PX4-Autopilot/docs
   ```

1. 安装依赖项（包括 Vitepress）：

   ```sh
   yarn install
   ```

1. 预览并运行库：

   ```sh
   yarn start
   ```

   - 一旦开发/预览服务器构建完成库（首次构建少于1分钟），它会显示您可以在其上预览站点的URL。
     这可能类似于：`http://localhost:5173/px4_user_guide/`。
   - 通过在终端提示中使用 **CTRL+C** 停止运行服务。

1. 在本地编辑器中打开预览的页面：

   在调用 `yarn start` 预览库之前，首先通过 `EDITOR` 环境变量指定本地文本编辑器文件。
   例如，您可以通过输入以下命令将 VSCode 设置为默认编辑器：

   - Windows：

     ```sh
     set EDITOR=code
     ```

   - Linux：

     ```sh
     export EDITOR=code
     ```

   此时每页底部的 **Open in your editor** 链接将用该编辑器打开当前页面（这将替换 _Open in GitHub_ 链接）。

1. 您可以按照部署方式构建库：

   ```sh
   # Ubuntu
   yarn docs:build

   # Windows
   yarn docs:buildwin
   ```

:::tip
使用 `yarn start` 可以在您进行更改时实时预览（文档会快速更新并运行）。
在提交PR之前，还应使用 `yarn docs:build` 进行构建，因为这可能会发现 `yarn start` 时未显示的问题。
:::

### 源代码结构

本指南使用 [Vitepress](https://vitepress.dev/) 工具链。

总体结构如下：

- 页面使用 Markdown 格式单独编写。
  - 语法与 GitHub Wiki 几乎相同。
  - Vitepress 也支持部分 [markdown 扩展](https://vitepress.dev/guide/markdown#markdown-extensions)。
    我们尽量避免使用这些扩展，仅在 [提示、警告等](https://vitepress.dev/guide/markdown#default-title) 情况下使用。
    这一点可能会重新评估——因为有些扩展功能非常有趣！
- 这是一个 [多语言](https://vitepress.dev/guide/i18n) 书籍：
  - 每种语言的页面存储在对应语言代码命名的文件夹中（例如："en" 为英文，"zh" 为中文，"ko" 为韩文）。
  - 仅编辑英文（`/en`）版本的文件。
    我们使用 [Crowdin](../contribute/translation.md) 管理翻译。
- 所有页面必须位于 `/en` 的适当命名的子文件夹中（例如，当前页面位于文件夹 `en/contribute/` 中）。
  - 这使得链接更简单，因为其他页面和图片始终处于相同的相对路径层级。
- 书籍的 _结构_ 由 `SUMMARY.md` 定义。

  - 如果你向指南中添加新页面，必须同时在此文件中添加条目！

    :::tip
    这并不是“标准 Vitepress”定义侧边栏的方式（摘要文件通过 [.vitepress/get_sidebar.js](https://github.com/PX4/PX4-user_guide/blob/main/.vitepress/get_sidebar.js) 引入）。
    :::

- 图片必须存储在 `/assets` 的子文件夹中。
  这个位置距离内容文件夹有两级目录，因此添加图片时需使用如下引用格式：

  ```plain
  ![图片描述](../../assets/路径/filename.jpg)
  ```

- 名为 **package.json** 的文件定义了构建所需的依赖项。
- 使用网络钩子跟踪每次文件合并到主分支时的操作，触发书籍重新构建。

### 添加新页面

当你添加新页面时，必须同时将其添加到 `en/SUMMARY.md` 中！

## 样式指南

1. 文件/文件名

   - 将新的markdown文件放入`/en/`的适当子文件夹中（例如`/en/contribute/`）。  
     不要使用更深层次的文件夹嵌套。
   - 将新的图片文件放入`/assets/`的适当嵌套子文件夹中。  
     允许且鼓励更深的嵌套层级。
   - 使用描述性的文件夹和文件名。  
     特别是图片文件名应描述其内容（不要命名为"image1.png"）。
   - 使用小写文件名，用下划线（`_`）分隔单词。

2. 图片

   - 使用最小尺寸和最低分辨率，同时保持图片的可用性（这会减少带宽较差用户的下载成本）。
   - 新图片应创建在`/assets/`的子文件夹中（以便在翻译间共享）。
   - SVG文件优先用于图表。  
     PNG文件优先于JPG用于截图。

3. 内容：

   - 一致且谨慎地使用"样式"（**加粗**、_斜体_等），尽可能少用。
     - **加粗**用于按钮按压和菜单定义。
     - _斜体_用于工具名称，如_QGroundControl_或_prettier_。
     - `代码`用于文件路径、代码、未链接的参数名，以及命令行工具，例如`prettier`。
   - 标题和页面标题应使用"首字母大写"。
   - 页面标题应使用一级标题（`#`）。  
     所有其他标题应为二级标题（`##`）或更低层级。
   - 不要对标题添加任何样式。
   - 不要翻译`info`、`tip`或`warning`声明的名称指示文本（例如`::: tip`），因为需要精确文本以正确渲染侧边栏。
   - 优先按句子断行。  
     不要基于任意行长度断行。
   - 使用_prettier_格式化（_VSCode_可通过扩展实现此功能）。

4. 视频：

   - 可通过格式`<lite-youtube videoid="<youtube-video-id>" title="your title"/>`添加YouTube视频（通过[https://www.npmjs.com/package/lite-youtube-embed](https://www.npmjs.com/package/lite-youtube-embed)自定义元素支持，该元素还支持其他参数）。
     - 教程视频应谨慎使用，因为它们过时快且难以维护。
     - 机体飞行的精彩视频始终受欢迎。

## 我在何处添加更改？

将新文件添加到涵盖类似主题的文件夹中。
然后在侧边栏 (`/en/SUMMARY.md`) 中按照现有结构添加引用！

## 翻译

有关翻译的信息请参见：[翻译](../contribute/translation.md)。

## 许可

所有PX4/Dronecode文档均可在许可的[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)许可证条款下免费使用和修改。