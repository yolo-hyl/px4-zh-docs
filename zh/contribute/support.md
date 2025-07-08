# 支持

<script setup>
import { useData } from 'vitepress'
const { site } = useData();
</script>

<div v-if="site.title !== 'PX4 Guide (main)'">
  <div class="custom-block danger">
    <p class="custom-block-title">此页面可能已过时。<a href="https://docs.px4.io/main/en/contribute/support.html">查看最新版本</a>。</p>
  </div>
</div>

本节内容展示如何从核心开发团队和更广泛的社区获取帮助。

## 论坛和聊天

核心开发团队和社区活跃在以下渠道：

- [PX4 Discuss Forum](https://discuss.px4.io/) - 请首先在这里发帖！
- [PX4 Discord](https://discord.gg/dronecode) - 如果在Discuss中几天内未获得回复，请在这里发帖（请附上论坛帖子链接）。

:::tip
Discuss论坛是首选渠道，因为它被搜索引擎收录并作为公共知识库。
:::

## 问题诊断

如果您不确定问题所在并需要帮助诊断时：

- 将日志上传至[Flight Log Review](https://logs.px4.io/)
- 在[PX4 Discuss](https://discuss.px4.io/c/flight-testing/)开启讨论，包含飞行报告和日志链接
- 如果问题是由于bug引起，开发团队可能会要求您[提交问题](#issue-bug-reporting)

## 问题与错误报告

- 将日志上传至[Flight Log Review](https://logs.px4.io/)
- [在Github提交Issue](https://github.com/PX4/PX4-Autopilot/issues)
  必须包含尽可能详细的飞行报告（需包含问题可复现所需信息）和Flight Review上的日志链接

## 每周开发会议

:::tip
开发者欢迎参加[每周开发会议](../contribute/dev_call.md)（及其他[开发者活动](../index.md#calendar-events)）以更深入参与项目。
:::

[Dev Call](../contribute/dev_call.md)是PX4开发团队每周举行的会议，用于讨论平台技术细节、协调工作和进行深入分析。

议程中还包含讨论拉取请求、重大影响问题和问答环节的时间。