# 查找/更新参数

PX4 的行为可以通过 [参数](../advanced_config/parameter_reference.md)（例如 [多旋翼 PID 参数](../config_mc/pid_tuning_guide_multicopter.md)、校准信息等）进行配置和调整。

_QGroundControl 参数_ 界面允许您查找和修改与机体相关的 **任何** 参数。  
该界面可通过点击 **Q** 应用程序图标 > **机体设置**，然后在侧边栏中选择 _Parameters_ 进入。

::: info
大多数常用参数更方便通过专用设置界面进行配置，如 [标准配置](../config/index.md) 部分所述。  
当需要修改较少修改的参数（例如在调试新机体时）时，_参数_ 界面是必要的。
:::

:::warning
尽管某些参数可以在飞行中修改，但不建议这样做（除非指南中明确说明）。
:::

<a id="finding"></a>

## 查找参数

您可以通过在 _Search_ 字段中输入关键词来搜索参数。  
这将显示所有包含该子字符串的参数名称和描述（点击 **Clear** 重置搜索，并使用 **Show modified only** 复选框过滤未更改的参数）。

![参数搜索](../../assets/qgc/setup/parameters/parameters_search.png)

您也可以通过点击左侧按钮按类型和分组浏览参数（如下图中 _Standard_ 参数的 _DShot_ 分组被选中）。

![参数界面](../../assets/qgc/setup/parameters/parameters_px4.png)

您可以展开/折叠“类型”分组。  
请注意底部名为 _Component X_ 的分组是连接的 [DroneCAN 外设](../dronecan/index.md#qgc-cannode-parameter-configuration)（"X" 是节点 ID）。  
如果这些外设在 QGC 启动时连接到飞控，[QGC 可以设置它们的参数](../dronecan/index.md#qgc-cannode-parameter-configuration)。

![参数类型 - 折叠视图](../../assets/qgc/setup/parameters/parameters_types.png)

:::tip
如果找不到预期的参数，请参见 [下一节](#missing)。
:::

<a id="missing"></a>

## 缺失的参数

参数通常不可见是因为它们依赖于其他参数（条件参数），或者固件中未包含该参数（见下文）。

### 条件参数

如果某个参数依赖于未启用的其他参数，该参数可能不会显示。

您通常可以通过搜索 [完整的参数参考](../advanced_config/parameter_reference.md) 和其他文档来判断参数是否为条件参数。  
特别是 [串口配置参数](../peripherals/serial_configuration.md) 取决于分配给串口的服务。

### 固件中无该参数

参数可能不在固件中，因为您使用的是不同版本的 PX4，或者构建时未包含关联模块。

每个 PX4 版本都会添加新参数，有时会删除或重命名现有参数。  
您可以通过查看 [完整参数参考](../advanced_config/parameter_reference.md) 确认目标版本的参数是否应存在。  
您也可以在源代码树和发行说明中搜索该参数。

另一个参数可能不在固件中的原因是其关联模块未包含。  
这在 _FMUv2 固件_ 中尤为常见，该固件省略了许多模块以使 PX4 适应可用的 1MB 闪存。  
有两种解决方法：

- 检查是否可以将开发板升级到包含所有模块的 FMUv3 固件：[固件 > FMUv2 引导加载程序更新](../config/firmware.md#bootloader)
- 如果开发板只能运行 FMUv2 固件，则需要 [重新构建 PX4](../dev_setup/building_px4.md) 并启用缺失的模块。  
  您需要通过 `make px4_fmuv2_default boardconfig` 重新配置 PX4 固件，并启用/禁用模块。

  ::: info
  您可能还需要禁用其他模块以使重新构建的固件适应 1MB 闪存。  
  选择要移除的模块需要一些尝试，并取决于机体需要满足的使用场景。
  :::

<a id="changing"></a>

## 修改参数

要修改参数值，请点击分组或搜索列表中的参数行。  
这将打开一个侧边对话框，您可以在其中更新值（该对话框还会提供参数的详细信息，包括更改是否需要重启才能生效）。

![修改参数值](../../assets/qgc/setup/parameters/parameters_changing.png)

::: info
点击 **Save** 后，参数会自动上传到连接的机体。  
根据参数不同，可能需要重启飞控使更改生效。
:::

## 工具

您可以从屏幕右上角的 **Tools** 菜单中选择其他选项。

![工具菜单](../../assets/qgc/setup/parameters/parameters_tools_menu.png)

**Refresh**  
通过重新请求所有参数值来刷新界面。

**Reset all to firmware defaults**  
将所有参数重置为固件的原始默认值。

**Reset to vehicle's configuration defaults**  
将所有参数重置为所选机型配置的原始默认值。

**Load from file / Save to file**  
从现有文件加载参数或将当前参数设置保存到文件。

**Clear all RC to Param**  
清除所有遥控器控制与参数的关联。  
更多信息请参见：[遥控器设置 > 参数调节通道](../config/radio.md#param-tuning-channels)。

**Reboot Vehicle**  
重启机体（某些参数更改后需要此操作）。