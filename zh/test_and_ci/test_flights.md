# 测试飞行

<script setup>
import { useData } from 'vitepress'
const { site } = useData();
</script>

<div v-if="site.title !== 'PX4 Guide (main)'">
  <div class="custom-block danger">
    <p class="custom-block-title">此页面可能已过期。 <a href="https://docs.px4.io/main/en/test_and_ci/test_flights.html">请查看最新版本</a>。</p>
  </div>
</div>

测试飞行对于质量保证非常重要。

在提交 [Pull Requests](../contribute/code.md#pull-requests) 时，无论是新功能还是错误修复，都应该提供关于该功能相关测试的说明，以及配套的飞行日志。

对于系统的重要变更，还应使用以下测试卡片运行一般性飞行测试。

## 测试卡片

这些测试卡片定义了"标准"飞行测试。
这些测试由测试团队在发布测试和更重大的系统变更时运行。

- [MC_01 - 手动模式](../test_cards/mc_01_manual_modes.md)
- [MC_02 - 完全自动](../test_cards/mc_02_full_autonomous.md)
- [MC_03 - 自动手动混合](../test_cards/mc_03_auto_manual_mix.md)
- [MC_04 - 故障安全测试](../test_cards/mc_04_failsafe_testing.md)
- [MC_05 - 室内飞行（手动模式）](../test_cards/mc_05_indoor_flight_manual_modes.md)