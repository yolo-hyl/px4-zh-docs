# Failsafe状态机模拟

<Badge type="tip" text="PX4 v1.14" />

本页面可用于模拟PX4 failsafe状态机在所有可能配置和条件下的行为。

该模拟在浏览器中实时运行与机体上相同的代码（模拟会自动与最新代码版本保持同步）。
请注意任何延迟动作(`COM_FAIL_ACT_T`)在模拟中也会被延迟。

使用方法：

1. 首先在左侧配置参数
   初始值对应PX4默认值
2. 设置机体类型
3. 在**State**中设置其他值或**Conditions**下的任意标志
   - **Intended Mode**对应通过遥控器或GCS（或外部脚本）的指令模式
     在发生fail-safe时状态机可能会覆盖该模式
4. 在**Output**中检查动作
5. 检查切换模式时会发生什么或**移动遥控器摇杆**
6. 尝试不同的设置和条件！

模拟也可以在本地执行以测试特定版本或修改集：

```sh
make run_failsafe_web_server
```

<iframe :src="withBase('/config/failsafe/index.html')" frameborder="0" height="1400px" style="text-align: center; margin-left: -20px; margin-right: -230px;" width="1200"></iframe>

<script setup>
import { withBase } from 'vitepress';
</script>