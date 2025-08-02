# GIT 示例

<a id="contributing_code"></a>

## 为PX4贡献代码

向PX4添加新功能需遵循定义的流程。要分享你的贡献，可以按照以下示例操作：

- [注册](https://github.com/join) GitHub账户（如尚未注册）
- 分叉PX4-Autopilot仓库（参见[这里](https://docs.github.com/en/get-started/quickstart/fork-a-repo)）
- 将分叉的仓库克隆到本地计算机

  ```sh
  cd ~/wherever/
  git clone https://github.com/<your git name>/PX4-Autopilot.git
  ```

- 进入新目录，初始化并更新子模块，添加原始上游PX4-Autopilot

  ```sh
  cd PX4-Autopilot
  git submodule update --init --recursive
  git remote add upstream https://github.com/PX4/PX4-Autopilot.git
  ```

- 现在你应拥有两个远程仓库：一个名为`upstream`的仓库指向PX4/PX4-Autopilot，另一个仓库`origin`指向你的分叉副本
- 可通过以下命令验证：

  ```sh
  git remote -v
  ```

- 对当前main分支进行你希望添加的修改
- 创建一个具有代表性的新分支名称

  ```sh
  git checkout -b <your feature branch name>
  ```

  可使用`git branch`命令确认当前所在分支

- 通过添加相应文件将希望提交的更改纳入版本控制

  ```sh
  git add <file name>
  ```

  如果偏好图形界面操作，可参考[Gitk](https://git-scm.com/book/en/v2/Git-in-Other-Environments-Graphical-Interfaces)或使用`git add -p`工具

- 用有意义的提交信息提交修改

  ```sh
  git commit -m "<your commit message>"
  ```

  提交信息规范请参阅[源代码管理](../contribute/code.md#commits-and-commit-messages)章节

- 经过一段时间后，[上游main分支](https://github.com/PX4/PX4-Autopilot.git)可能已更新
  PX4偏好线性提交历史，使用[git rebase](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)进行更新
  要将上游最新更改合并到本地分支，请先切换到main分支

  ```sh
  git checkout main
  ```

  然后从上游main拉取最新提交

  ```sh
  git pull upstream main
  ```

  现在本地main分支已更新
  切换回特性分支并基于更新后的main进行rebase

  ```sh
  git checkout <your feature branch name>
  git rebase main
  ```

- 现在可以将本地提交推送到分叉仓库

  ```sh
  git push origin <your feature branch name>
  ```

- 可通过浏览器访问你的分叉仓库验证推送是否成功：`https://github.com/<your git name>/PX4-Autopilot.git`
  应该会看到新分支已推送到分叉仓库的提示信息

- 现在可以创建拉取请求（PR）
  在"新分支提示"右侧（见上一步），应看到绿色按钮"Compare & Create Pull Request"
  系统会列出你的更改，此时需要添加有意义的标题（单次提交PR通常使用提交信息）和说明（<span style="color:orange">说明你做了什么及原因</span>。可参考[其他拉取请求](https://github.com/PX4/PX4-Autopilot/pulls)进行对比）
- 完成！
  PX4负责人将审查你的贡献并决定是否集成
  请定期查看他们是否就你的修改提出问题
  
## 切换源代码树

我们建议使用 PX4 的 `make` 命令在源代码分支之间切换。  
这可以避免你记住更新子模块和清理构建产物的命令（未清理的构建文件在切换后会导致 "untracked files" 错误）。

切换分支步骤：

1. 清理当前分支，反初始化子模块并删除所有构建产物：

   ```sh
   make clean
   make distclean
   ```

1. 切换到新分支或标签（此处我们首先从 `upstream` 远程仓库获取虚构分支 "PR_test_branch"）：

   ```sh
   git fetch upstream PR_test_branch
   git checkout PR_test_branch
   ```

1. 获取新分支的子模块：

   ```sh
   make submodulesclean
   ```

<!-- FYI: Cleaning commands in https://github.com/PX4/PX4-Autopilot/blob/main/Makefile#L494 -->

## 获取特定版本

特定 PX4 版本发布以 [发布分支](#获取发布分支) 的标签形式存在，命名格式为 `v<release>`。
这些版本可通过 [Github 页面查看](https://github.com/PX4/PX4-Autopilot/releases?q=release&expanded=true)（也可通过 `git tag -l` 查询所有标签）。

要获取 _特定旧版本_ 的源代码（标签）：

1. 克隆 PX4-Autopilot 仓库并进入 _PX4-Autopilot_ 目录：

   ```sh
   git clone https://github.com/PX4/PX4-Autopilot.git
   cd PX4-Autopilot
   ```

   ::: info

   可复用现有仓库而非克隆新仓库。
   此时需要清理构建环境（参见 [源代码树切换](#切换源代码树)）：

   ```sh
   make clean
   make distclean
   ```

   :::

1. 切换到特定标签的代码（例如标签 v1.13.0-beta2）

   ```sh
   git checkout v1.13.0-beta2
   ```

1. 更新子模块：

   ```sh
   make submodulesclean
   ```

## 获取发布分支

发布分支是从 `main` 分支创建的，用于将必要的更改从 main 分支回溯到发布版本中。  
分支的命名格式为 `release/<release_number>`（例如 `release/v1.13`），[分支列表请查看此处](https://github.com/PX4/PX4-Autopilot/branches/all?query=release)。

获取发布分支的步骤：

- 克隆 PX4-Autopilot 仓库并进入 _PX4-Autopilot_ 目录：

  ```sh
  git clone https://github.com/PX4/PX4-Autopilot.git
  cd PX4-Autopilot
  ```

  ::: info

  可以复用已存在的仓库，而无需重新克隆。  
  在这种情况下需要清理构建环境（详见 [更改源代码树](#切换源代码树)）：

  ```sh
  make clean
  make distclean
  ```

  :::

- 获取所需的发布分支。  
  例如，假设需要 PX4 v1.14 的源代码：

  ```sh
  git fetch origin release/1.14
  ```

- 切换到该分支代码：

  ```sh
  git checkout release/1.14
  ```

- 更新子模块：

  ```sh
  make submodulesclean
  ```

## 更新子模块

有几种方式可以更新子模块。  
你可以克隆仓库，或者进入子模块目录并按照 [向PX4贡献代码](#contributing_code) 中相同的流程操作。

## 为子模块更新做 PR

在您已经为子模块 X 仓库提交了 PR 并且错误修复/功能添加已包含在子模块 X 的当前 main 分支中后，需要执行此操作。由于固件仍指向更新前的提交记录，因此需要创建子模块拉取请求，使固件使用的子模块指向最新提交。

```sh
cd Firmware
```

- 创建一个新分支，描述子模块更新的修复/功能：

  ```sh
  git checkout -b pr-some-fix
  ```

- 进入子模块子目录

  ```sh
  cd <path to submodule>
  ```

- PX4 子模块可能未指向最新提交。因此，首先切换到 main 分支并拉取最新的上游代码。

  ```sh
  git checkout main
  git pull upstream main
  ```

- 返回 Firmware 目录，然后按照常规步骤添加、提交并推送更改。

  ```sh
  cd -
  git add <path to submodule>
  git commit -m "Update submodule to include ..."
  git push upstream pr-some-fix
  ```

## 检查拉取请求

即使该合并的分支仅存在于该用户的派生仓库中，您也可以测试某人的拉取请求（更改尚未合并）。请执行以下操作：

```sh
git fetch upstream  pull/<PR ID>/head:<branch name>
```

`PR ID` 是拉取请求标题旁边的编号（不带 #），而 `<branch name>` 也可以在 `PR ID` 下方找到，例如 `<其他人的Git名称>:<branch name>`。之后，您可以在本地通过以下命令查看新创建的分支：

```sh
git branch
```

然后切换到该分支

```sh
git checkout <branch name>
```

## 常见问题

### 向分叉仓库强制推送代码

首次提交PR后，PX4社区成员将审查你的修改。大多数情况下你需要根据审查意见调整本地分支。本地文件修改完成后，需要将特性分支重新基于最新的upstream/main进行变基。但变基完成后，将无法直接向你的分叉仓库推送特性分支，而是需要使用强制推送：

```sh
git push --force-with-lease origin <your feature branch name>
```

### 变基时的合并冲突

如果在`git rebase`过程中出现冲突，请参考 [此指南](https://docs.github.com/en/get-started/using-git/resolving-merge-conflicts-after-a-git-rebase)。

### 拉取时的合并冲突

如果在`git pull`过程中出现冲突，请参考 [此指南](https://help.github.com/articles/resolving-a-merge-conflict-using-the-command-line/#competing-line-change-merge-conflicts)。

### 因git标签过期导致的构建错误

当git标签过期时会出现构建错误 `Error: PX4 version too low, expected at least vx.x.x`。

可以通过获取上游仓库的标签来解决：

```sh
git fetch upstream --tags
```