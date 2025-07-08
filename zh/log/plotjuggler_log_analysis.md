# 使用 PlotJuggler 进行日志分析

[PlotJuggler](https://github.com/facontidavide/PlotJuggler) 可用于分析 ULogs，以进行深入开发工作。  
它非常有用，因为所有 uORB 主题都可以被暴露/绘图，并且具有自定义函数来修改数据（例如，将四元数值转换为滚转/俯仰/偏航）。

## 安装

你可以在[这里](https://github.com/facontidavide/PlotJuggler/releases)找到 Plot Juggler 的最新版本。

#### Windows 发行版注意事项

最新版本的 PlotJuggler 可能在 Windows 上无法运行。  
在这种情况下，回退到 [v2.8.4](https://github.com/facontidavide/PlotJuggler/releases/tag/2.8.4)（已知在 Windows 上可以正常工作）。

#### Linux 发行版的 AppImage 注意事项

如果下载的 AppImage 无法打开，可能需要更改其访问权限。  
这可以通过终端使用以下命令完成：

```sh
chmod 777 <Path-To-PlotJuggler-AppImage>
```

## 一般使用

最常见的两个任务是“搜索”记录的 uORB 主题，以及“拖放”特定主题中的字段到图表视图。  
这些在下图中进行了演示。

![Plot Juggler 基本使用](../../assets/flight_log_analysis/plot_juggler/plotjuggler_timeseries_search_and_drop.svg)

### 水平/垂直分割 : 多面板

最强大的功能之一是将屏幕水平/垂直分割，并同时显示不同的图表（顶部有同步的时间条，当你在底部移动时间光标时，所有图表时间条同步移动）。

这在下图中进行了演示：

![Plot Juggler 多面板演示](../../assets/flight_log_analysis/plot_juggler/plotjuggler_dragdrop_multipanel.gif)

在示例中，首先通过将屏幕分为3部分，绘制了 `vehicle_local_position` 主题的 `ax`、`ay` 和 `az`（加速度估计）组件。  
然后在右侧面板下方添加了 `vz`（速度估计）组件，最后在中间下方面板绘制了 `battery_status` 主题的 `current_a`（电池电流）组件。

虽然一开始可能不明显，但可以观察到每当机体开始移动（电池电流值升高）时，加速度和速度值也会开始变化。  
这是因为所有数据都以时间序列形式显示，每个值对应特定时间戳。

这有助于提供事件发生及其原因的总体视图。  
仅通过查看一个图表很难排查问题，但通过显示多个图表，可以更容易地了解系统中发生的情况。

### 以2D方式显示数据

另一个强大功能是能够以散点图方式在XY平面上显示2D数据（每个数据在X、Y轴上）。  
通过按住 `Ctrl` 键选择两个数据点（例如 `vehicle_local_position` 主题的 `x` 和 `y` 组件），然后按住 `右键鼠标` 拖放来实现。

![Plot Juggler 2D绘图](../../assets/flight_log_analysis/plot_juggler/plotjuggler_2d_graph.gif)

在示例中，将机体在局部坐标系中的位置估计值绘制在XY平面上，显示了估计位置的2D视图，右侧绘制了 `vx` 和 `vy` 组件（速度估计），下方分割视图中绘制了 `vz`（垂直速度估计）。

这直观地展示了位置与速度之间的关系。  
例如，注意当机体沿X轴移动时，`vx` 值升高，当机体转向Y轴方向时，`vy` 值也开始变化。

#### 使用 'Play' 按钮

此处使用 **Play** 按钮以实时速度播放记录的数据（可在右下角调整速度因子）。  
这详细展示了上述位置/速度关系。

![Plot Juggler 2D 深度分析](../../assets/flight_log_analysis/plot_juggler/plotjuggler_2d_graph_pos_vel_analysis.gif)

::: info
通过下载上面使用的 ULog 和布局文件，亲自尝试船只测试日志分析！
- [船只测试 ULog](https://github.com/PX4/PX4-user_guide/raw/main/assets/flight_log_analysis/plot_juggler/sample_log_boat_testing_2022-7-28-13-31-16.ulg)
- [船只测试分析布局](https://raw.githubusercontent.com/PX4/PX4-user_guide/main/assets/flight_log_analysis/plot_juggler/sample_log_boat_testing_layout.xml)
:::

## 布局模板

PX4 开发者共享了多个 PlotJuggler 布局文件。  
每个文件可用于特定目的（多旋翼调参、垂直起降调参、船只调试等）：

* [示例视图布局](https://github.com/PX4/PX4-user_guide/blob/main/assets/flight_log_analysis/plot_juggler/plotjuggler_sample_view.xml) : 用于上面的 [多面板示例](#splitting-horizontally-vertically-multi-panel) 的模板。

## 高级用法

### 使用 LUA 脚本创建自定义时间序列

Plot Juggler 支持使用 LUA 脚本处理和显示数据。  
这是一个强大的功能，可以执行诸如积分曲线、平均两条曲线、去除偏移等操作。

#### 从四元数计算滚转/俯仰/偏航

![使用 Lua 脚本将四元数转换为滚转](../../assets/flight_log_analysis/plot_juggler/plotjuggler_quaternion_to_roll_lua_script.png)

为了了解机体的姿态，PX4 在 `vehicle_attitude` 主题中以浮点数数组的形式记录了估计姿态的四元数（q[4]）。  
由于这些值不提供上下文信息（例如 `roll`），需要通过三角函数进行转换。

1. 在左侧的时间序列列表面板中搜索 `vehicle_attitude` 主题  
2. 通过点击 `q.00`，然后按住 Shift + 点击 `q.03`，选择4个四元数成员（`q.00, q.01, q.02, q.03`）  
3. 在左下角的“自定义序列”部分点击 '+' 符号创建新序列  
4. 再次选择4个四元数成员  
5. 在脚本中实现四元数到滚转/俯仰/偏航的转换逻辑  

绘制结果如图所示，其中 `quat_to_roll` 函数实现了四元数到滚转角的转换。