# 源代码管理## 分支模型

PX4 项目使用一个包含三个分支的 Git 分支模型：

- [main](https://github.com/PX4/PX4-Autopilot/tree/main) 默认不稳定，开发节奏较快。
- [beta](https://github.com/PX4/PX4-Autopilot/tree/beta) 已经过充分测试，专为飞行测试人员设计。
- [stable](https://github.com/PX4/PX4-Autopilot/tree/stable) 指向最新发布的版本。

我们尽量通过 [rebase 保持线性历史记录](https://www.atlassian.com/git/tutorials/rewriting-history)，并避免使用 [GitHub Flow](https://docs.github.com/en/get-started/quickstart/github-flow)。
但由于全球化团队和快速迭代的特性，有时不得不使用合并操作。

要贡献新功能，请先 [注册 GitHub 账号](https://docs.github.com/en/get-started/signing-up-for-github/signing-up-for-a-new-github-account)，然后 [fork 仓库](https://docs.github.com/en/get-started/quickstart/fork-a-repo)，[创建新分支](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-and-deleting-branches-within-your-repository)，添加你的 [提交记录](#commits-and-commit-messages)，最后 [发送拉取请求](#pull-requests)。
当代码通过我们的 [持续集成](https://en.wikipedia.org/wiki/Continuous_integration) 测试后将会被合并。

所有代码贡献必须采用宽松的 [BSD 3-clause license](https://opensource.org/licenses/BSD-3-Clause) 协议授权，且代码不得对使用施加任何额外限制。

## 代码风格

PX4 使用 [Google C++ 风格指南](https://google.github.io/styleguide/cppguide.html)，并做了以下（最小）修改：

::: info

并非所有 PX4 源代码都符合该风格指南，但你编写的任何 _新代码_ 都应遵循该指南——无论是新文件还是已有文件。
如果你更新了现有文件，无需强制整个文件都符合风格指南，只需修改你所更改的部分即可。

:::

### 制表符

- 使用制表符进行缩进（等效于8个空格）。
- 使用空格进行对齐。

### 行长度

- 最大行长度为120个字符。

### 文件扩展名

- 源文件使用 `*.cpp` 扩展名，而非 `*.cc`。

### 函数和方法名称

- `lowerCamelCase()` 用于函数和方法名称，以_视觉_区分它们与 `ClassConstructors()` 和 `ClassNames`。

### 私有类成员变量名称

- 私有类成员变量名称使用 `_underscore_prefixed_snake_case`，而非 `underscore_postfixed_`。

### 类隐私关键字

- `public:`、`private:` 或 `protected:` 关键字前 _零_ 个空格。

### 示例代码片段

```cpp
class MyClass {
public:

        /**
         * @brief 该函数的功能描述。
         *
         * @param[in] input_param 输入参数的清晰描述 [单位]
         * @return 返回值描述 [单位]
         */
        float doSomething(const float input_param) const {
                const float in_scope_variable = input_param + kConstantFloat;
                return in_scope_variable * _private_member_variable;
        }

        void setPrivateMember(const float private_member_variable) { _private_member_variable = private_member_variable; }

        /**
         * @return "获取"的返回值描述 [单位]
         */
        float getPrivateMember() const { return _private_member_variable; }

private:

        // 如果常量名称不能完全明确其含义，请在此处清晰描述 [单位]
        static constexpr float kConstantFloat = ...;

        // 如果变量名称不能完全明确其含义，请在此处清晰描述 [单位]
        float _private_member_variable{...};
};
```

## 源代码中的文档

PX4 开发人员被鼓励在源代码中创建适当的文档。

::: info

源代码文档标准并未强制执行，当前代码的文档编写存在不一致的情况。
我们希望做得更好！

:::

目前我们有两种基于源代码的文档类型：

- `PRINT_MODULE_*` 方法同时用于模块运行时使用说明和本指南中的 [模块与命令参考](../modules/modules_main.md)。
  - API 文档在[此处源代码中](https://github.com/PX4/PX4-Autopilot/blob/v1.8.0/src/platforms/px4_module.h#L381)。
  - 优秀的使用示例包括 [应用程序/模块模板](../modules/module_template.md) 和模块参考中链接的文件。
- 我们鼓励在源代码中添加文档 _当其能提供额外价值/不重复时_。

  :::tip

  开发人员应命名 C++ 实体（类、函数、变量等），使其目的可被推断 - 减少对显式文档的需求。

  :::

  - 不要添加可通过 C++ 实体名称直接推断的文档。
  - 在定义变量、常量和输入/返回参数时，始终指定单位。
  - 常见情况下需要添加关于边界情况和错误处理的信息。
  - 如果需要文档，请使用 [Doxgyen](http://www.doxygen.nl/) 标签：`@class`、`@file`、`@param`、`@return`、`@brief`、`@var`、`@see`、`@note`。
    一个优秀的使用示例是 [src/modules/events/send_event.h](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/events/send_event.h)。

请避免使用“魔法数字”，例如，条件中的这个数字来自哪里？关于偏航杆输入的乘数呢？

```cpp
if (fabsf(yaw_stick_normalized_input) < 0.1f) {
        yaw_rate_setpoint = 0.0f;
}
else {
        yaw_rate_setpoint = 0.52f * yaw_stick_normalized_input;
}
```

相反，应在头文件中将其定义为具有适当上下文的命名常量：

```cpp
// 归一化偏航杆输入的死区阈值
static constexpr float kYawStickDeadzone = 0.1f;

// [rad/s] 归一化偏航杆输入的死区阈值
static constexpr float kMaxYawRate = math::radians(30.0f);
```

并更新源代码实现。

```cpp
if (fabsf(yaw_stick_normalized_input) < kYawStickDeadzone) {
        yaw_rate_setpoint = 0.0f;
}
else {
        yaw_rate_setpoint = kMaxYawRate * yaw_stick_normalized_input;
}
```

## 提交记录和提交信息

所有非简单修改都应使用描述性、多段落的提交信息。
合理结构化这些信息，使其在单行摘要中有意义，同时提供完整细节。

```plain
组件: 用一句话解释修改内容。Fixes #1234

在摘要行开头添加软件组件前缀，可通过模块名或描述性名称添加。
（例如："mc_att_ctrl" 或 "multicopter attitude controller"）。

如果问题编号以 <Fixes #1234> 形式附加，GitHub
会在提交合并到 master 分支时自动关闭该问题。

消息正文可包含多个段落。
详细描述你修改了什么。链接相关问题和飞行
日志，无论是与此次修复相关还是与此次提交的
测试结果相关。

描述修改内容及修改原因，避免仅复述代码变更（良好示例："为使用低质量GPS接收的机体添加额外安全检查"。
不良示例："添加 gps_reception_check() 函数"）。

Reported-by: 姓名 <email@px4.io>
```

**使用 **`git commit -s`** 对所有提交进行签名。** 这将添加 `signed-off-by:` 与你的姓名和邮箱作为最后一行。

本提交指南基于Linux内核和其他 [由Linus Torvalds维护的项目](https://github.com/torvalds/subsurface-for-dirk/blob/a48494d2fbed58c751e9b7e8fbff88582f9b2d02/README#L88-L115) 的最佳实践。

## 拉取请求

Github [拉取请求（PRs）](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) 是向 PX4 提交新功能和错误修复的主要机制。

它们包含你的分支中相对于主分支的新 [提交](#commits-and-commit-messages) 集合，以及对更改的描述。

描述应包括：

- 变更带来的概述；足够理解代码的广泛目的
- 相关问题或支持信息的链接。
- 关于 PR 功能已进行的测试信息，附飞行日志链接。
- 如果可能，变更前后的 [测试飞行](../test_and_ci/test_flights.md) 通用测试结果。