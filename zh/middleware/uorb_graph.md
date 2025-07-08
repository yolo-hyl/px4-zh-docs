# uORB 发布/订阅图

本页提供了一个uORB发布/订阅图，展示了模块之间的通信关系。  
该图信息直接从源代码中提取。使用说明请参见[下方](#graph-properties)。

<iframe :src="withBase('/middleware/index.html')" frameborder="0" width="1300" height="1450px" style="text-align: center; margin-left: 0px; margin-right: 0px;"></iframe>

<script setup>
import { withBase } from 'vitepress';
</script>

## 图表属性

该图表具有以下特性：

- 模块以灰色圆角显示，主题显示为彩色矩形框。
- 相关模块与主题通过线条连接。  
  虚线表示模块发布该主题，实线表示模块订阅该主题，点划线表示模块同时发布和订阅该主题。
- 部分模块和主题被排除：  
  - 被多个模块订阅/发布的主题：`parameter_update`、`mavlink_log`和`log_message`。  
  - 日志主题集合。  
  - 无订阅者或无发布者的主题。  
  - **src/examples**目录下的模块。
- 将鼠标悬停在模块/主题上会高亮显示其所有连接。
- 双击某个主题可打开其消息定义。
- 确保浏览器窗口足够宽以显示完整图表（可通过左上角图标隐藏侧边栏菜单）。  
  您也可以放大/缩小图像。
- *预设*选择列表可筛选显示的模块范围。
- *搜索*框可用于查找特定模块/主题（未被选中的主题将显示为灰色）。