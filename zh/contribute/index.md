# 社区

<script setup>
import { useData } from 'vitepress'
const { site } = useData();
</script>

<div v-if="site.title !== 'PX4 Guide (main)'">
  <div class="custom-block danger">
    <p class="custom-block-title">此页面可能已过时。请查看<a href="https://docs.px4.io/main/en/contribute/">最新版本</a>。</p>
  </div>
</div>

欢迎来到PX4社区！

:::tip
我们承诺遵守[PX4行为准则](https://github.com/PX4/PX4-Autopilot/blob/main/CODE_OF_CONDUCT.md)，旨在营造开放友好的环境。
:::

本节包含如何与社区互动及为PX4做贡献的信息：

- [开发人员会议](../contribute/dev_call.md) - 与开发团队讨论架构、拉取请求和关键问题
- [维护人员](./maintainers.md) - PX4子系统和生态系统的维护者
- [支持](../contribute/support.md) - 获取帮助和提交问题
- [源代码管理](../contribute/code.md) - 参与PX4代码工作
- [文档](../contribute/docs.md) - 改进文档
- [翻译](../contribute/translation.md) - 使用Crowdin进行翻译
- [术语/符号](../contribute/notation.md) - 文档中使用的术语和符号
- [许可证](../contribute/licenses.md) - PX4和Pixhawk许可证