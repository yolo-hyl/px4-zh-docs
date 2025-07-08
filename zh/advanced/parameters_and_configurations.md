# 参数与配置

PX4 使用 _param 子系统_（由 `float` 和 `int32_t` 值组成的平面表）以及文本文件（用于启动脚本）来存储其配置。

本节将详细讨论 _param_ 子系统。  
内容涵盖如何列出、保存和加载参数，以及如何定义参数并使其对地面站可用。

:::tip
[System startup](../concept/system_startup.md) 和 [frame configuration](../dev_airframes/adding_a_new_frame.md) 启动脚本的工作方式详见其他页面。
:::

## 命令行使用

PX4 [系统控制台](../debug/system_console.md) 提供了 [param](../modules/modules_command.md#param) 工具，可用于设置参数、读取参数值、保存参数以及从文件导出和恢复参数。

### 获取和设置参数

`param show` 命令会列出所有系统参数：

```sh
param show
```

通过使用通配符 "*" 可以更精确地筛选参数名称：

```sh
nsh> param show RC_MAP_A*
Symbols: x = used, + = saved, * = unsaved
x   RC_MAP_AUX1 [359,498] : 0
x   RC_MAP_AUX2 [360,499] : 0
x   RC_MAP_AUX3 [361,500] : 0
x   RC_MAP_ACRO_SW [375,514] : 0

 723 parameters total, 532 used.
```

使用 `-c` 标志可显示所有已更改的参数（相对于默认值）：

```sh
param show -c
```

可使用 `param show-for-airframe` 显示当前机体定义文件中所有相对于默认值已更改的参数（及其导入的默认值）。

### 导出和加载参数

您可以保存所有已更改的参数（即与机体默认值不同的参数）。

标准的 `param save` 命令会将参数存储在当前默认文件中：

```sh
param save
```

如果提供参数，会将参数保存到指定的新位置：

```sh
param save /fs/microsd/vtol_param_backup
```

加载参数有两个不同的命令：

- `param load` 首先会将所有参数重置为默认值，然后用文件中的值覆盖参数。
- `param import` 仅用文件中的值覆盖当前参数值，然后保存结果（即实质上调用 `param save`）。

`load` 实质上会将参数重置为保存时的状态（我们说“实质上”是因为文件中保存的参数会被更新，但其他参数可能具有与创建参数文件时不同的固件定义的默认值）。

相反，`import` 会将文件中的参数与机体的当前状态合并。例如，这可以用于仅导入包含校准数据的参数文件，而不会覆盖系统配置的其余部分。

以下示例展示了两种情况：# 将参数重置为文件保存时的状态
param load /fs/microsd/vtol_param_backup# 可选地保存参数（在加载时不会自动执行）
param save
```

```sh# 将已保存的参数与当前参数合并
param import /fs/microsd/vtol_param_backup## 创建/定义参数

参数定义包含两个部分：

- [参数元数据](#parameter-metadata) 指定了固件中每个参数的默认值，以及在地面控制站和文档中呈现（和编辑）参数的其他元数据。
- [C/C++ 代码](#c-c-api) 提供了从 PX4 模块和驱动中获取和/或订阅参数值的访问方式。

下面描述了编写元数据和代码的几种方法。
在可能的情况下，代码应优先使用较新的 [YAML 元数据](#yaml-metadata) 和 [C++ API](#c-api) 而非旧的 C 参数/代码定义，因为这些方法更灵活且更健壮。

参数元数据 [被编译到固件中](#publishing-parameter-metadata-to-a-gcs)，
并通过 [MAVLink组件信息服务](https://mavlink.io/en/services/component_information.html) 提供给地面站。

### 参数名称

参数名称不得超过16个ASCII字符。

按照惯例，组内每个参数应共享相同的（有意义的）字符串前缀，后跟下划线，`MC_` 和 `FW_` 用于特指多旋翼或固定翼系统的参数。
此约定并非强制。

代码和 [参数元数据](#parameter-metadata) 中的名称必须匹配，以正确关联参数及其元数据（包括固件中的默认值）。

### C / C++ API

有独立的 C 和 C++ API，可用于从 PX4 模块和驱动中访问参数值。

API 之间的一个重要区别是 C++ 版本具有更高效的标准化机制来与参数值更改同步（即来自地面站的更改）。

同步很重要，因为参数值可能在任何时候被修改。
您的代码应始终使用参数存储中的当前值。
如果无法获取最新版本，则修改参数后需要重新启动（使用 `@reboot_required` 元数据设置此需求）。

此外，C++ 版本还具有更好的类型安全性和更低的 RAM 开销。
缺点是参数名称必须在编译时已知，而 C API 可以接受动态生成的名称作为字符串。

#### C++ API

C++ API 提供宏来将参数声明为 _类属性_。
您添加一些"样板代码"以定期监听与 _任何_ 参数更新相关的 [uORB主题](../middleware/uorb.md)。
框架代码（不可见）会处理影响您的参数属性的 uORB 消息并使其保持同步。
在其余代码中，您可以直接使用定义的参数属性，它们将始终保持最新！

首先在模块或驱动的类头文件中包含所需的头文件：

- **px4_platform_common/module_params.h** 以获取 `DEFINE_PARAMETERS` 宏：

  ```cpp
  #include <px4_platform_common/module_params.h>
  ```

- **parameter_update.h** 以访问 uORB `parameter_update` 消息：

  ```cpp
  #include <uORB/topics/parameter_update.h>
  ```

- **Subscription.hpp** 用于 uORB C++ 订阅 API：

  ```cpp
  #include <uORB/Subscription.hpp>
  ```

从 `ModuleParams` 派生您的类，并使用 `DEFINE_PARAMETERS` 指定参数列表及其关联的参数属性。
参数名称必须与参数元数据定义中的名称相同。

```cpp
class MyModule : ..., public ModuleParams
{
public:
	...

private:

	/**
	 * 检查参数更改并更新它们（如果需要）。
	 */
	void parameters_update();

	DEFINE_PARAMETERS(
		(ParamInt<px4::params::SYS_AUTOSTART>) _sys_autostart,   /**< 示例参数 */
		(ParamFloat<px4::params::ATT_BIAS_MAX>) _att_bias_max  /**< 另一个参数 */
	)

	// 订阅
	uORB::SubscriptionInterval _parameter_update_sub{ORB_ID(parameter_update), 1_s};

};
```

在 cpp 文件中添加样板代码以检查与参数更新相关的 uORB 消息。

在代码中定期调用 `parameters_update();` 以检查是否有更新：

```cpp
void Module::parameters_update()
{
	if (_parameter_update_sub.updated()) {
		parameter_update_s param_update;
		_parameter_update_sub.copy(&param_update);

		// 如果有任何参数更新，调用 updateParams() 检查
		// 此类属性是否需要更新（并执行更新）。
		updateParams();
	}
}
```

在上述方法中：

- `_parameter_update_sub.updated()` 告知我们 `param_update` uORB 消息是否有任何更新（但不告知具体哪个参数）。
- 如果有"某些"参数更新，我们将更新复制到 `parameter_update_s`（`param_update`）中，以清除待处理的更新。
- 然后调用 `ModuleParams::updateParams()`。
  这个"幕后"操作会更新我们 `DEFINE_PARAMETERS` 列表中的所有参数属性。

参数属性（此处为 `_sys_autostart` 和 `_att_bias_max`）可用于表示参数，并在参数值更改时自动更新。

:::tip
[应用程序/模块模板](../modules/module_template.md) 使用新的 C++ API 但未包含 [参数元数据](#parameter-metadata)。
:::

#### C API

C API 可在模块和驱动中使用。

首先包含参数 API：

```C
#include <parameters/param.h>
```

然后检索参数并将其分配给变量（此处为 `my_param`），如下所示针对 `PARAM_NAME`。
变量 `my_param` 随后可在您的模块代码中使用。

```C
int32_t my_param = 0;
param_get(param_find("PARAM_NAME"), &my_param);
```

::: info
如果 `PARAM_NAME` 在参数元数据中声明，则其默认值将被设置，上述查找参数的调用应始终成功。
:::

`param_find()` 是一个"代价较高"的操作，它返回一个句柄，可由 `param_get()` 使用。
如果您需要多次读取参数，可以缓存句柄并在需要时在 `param_get()` 中使用。

```cpp
```# 获取参数的句柄
param_t my_param_handle = PARAM_INVALID;
my_param_handle = param_find("PARAM_NAME");# 在需要时查询参数值
int32_t my_param = 0;
param_get(my_param_handle, &my_param);
```

### 参数元数据

PX4 使用广泛的参数元数据系统来驱动面向用户的参数展示，并在固件中为每个参数设置默认值。

:::tip
正确的元数据对地面站在用户体验方面至关重要。
:::

参数元数据可以存储在源代码树的任何位置，作为 **.c** 或 **.yaml** 参数定义文件（YAML 定义是较新的方式，且更灵活）。
通常，它与相关模块一起存储。

构建系统会提取元数据（使用 `make parameters_metadata`）来生成[参数参考文档](../advanced_config/parameter_reference.md)以及[地面站使用的参数信息](#publishing-parameter-metadata-to-a-gcs)。

:::warning
添加新的参数文件后，应在构建前调用 `make clean` 以生成新参数（参数文件作为 _cmake_ 配置步骤的一部分添加，该步骤会在干净构建或修改 cmake 文件时执行）。
:::

#### YAML 元数据

::: info
截至当前写作时，YAML 参数定义无法在 _libraries_ 中使用。
:::

YAML 元数据旨在完全替代 **.c** 定义。
它支持所有相同的元数据，以及新特性如多实例定义。

- YAML 参数元数据模式在这里：[validation/module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)。
- YAML 定义的示例可在 MAVLink 参数定义中找到：[/src/modules/mavlink/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/module.yaml)。
- 通过在 `CMakeLists.txt` 文件的 `px4_add_module` 部分添加以下内容，可以将 YAML 文件注册到 cmake 构建系统中：

  ```cmake
  MODULE_CONFIG
  	module.yaml
  ```

#### 多实例（模板化）YAML 元数据

[YAML 参数定义](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)中支持模板化参数定义（模板化参数代码不支持）。

YAML 允许您使用 `${i}` 在参数名称、描述等中定义实例编号。
例如，以下内容将生成 MY_PARAM_1_RATE、MY_PARAM_2_RATE 等。

```yaml
MY_PARAM_${i}_RATE:
  description:
    short: 第 ${i} 个实例的最大速率
```

以下 YAML 定义提供了起始和结束索引：

- `num_instances`（默认 1）：生成的实例数量（>=1）
- `instance_start`（默认 0）：第一个实例编号。若为 0，`${i}` 展开为 [0, N-1]。

完整示例请参见 MAVLink 参数定义：[/src/modules/mavlink/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/module.yaml)

#### C 参数元数据

定义参数元数据的传统方法是在扩展名为 **.c** 的文件中（截至当前写作时，这在源代码树中是最常用的方法）。

参数元数据部分如下所示：

```cpp
/**
 * 俯仰 P 增益
 *
 * 俯仰比例增益，即 1 rad 误差对应的期望角速度（rad/s）。
 *
 * @unit 1/s
 * @min 0.0
 * @max 10
 * @decimal 2
 * @increment 0.0005
 * @reboot_required true
 * @group 多旋翼姿态控制
 */
PARAM_DEFINE_FLOAT(MC_PITCH_P, 6.5f);
```

```cpp
/**
 * 基于 GPS 速度的加速度补偿。
 *
 * @group 姿态 Q 估计器
 * @boolean
 */
PARAM_DEFINE_INT32(ATT_ACC_COMP, 1);
```

末尾的 `PARAM_DEFINE_*` 宏指定了参数类型（`PARAM_DEFINE_FLOAT` 或 `PARAM_DEFINE_INT32`）、参数名称（必须与代码中使用的名称匹配）以及固件中的默认值。

注释块中的所有行都是可选的，主要用于控制地面站中的显示和编辑选项。
每行的具体用途如下（更多细节请参见 [module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml))：

```cpp
/**
 * <标题>
 *
 * <更长的描述，可以是多行>
 *
 * @unit <单位，例如 m 表示米>
 * @min <最小合理值。可以被用户覆盖>
 * @max <最大合理值。可以被用户覆盖>
 * @decimal <最小合理值。可以被用户覆盖>
 * @increment <此值在 UI 中的增量“步长”>
 * @reboot_required true <如果更改此参数需要系统重启，请添加此标记>
 * @boolean <如果整数参数表示布尔值，请添加此标记>
 * @group <为构成组的参数指定标题>
 */
```

## 发布参数元数据到地面站

参数元数据 JSON 文件被编译到固件中（或托管在互联网上），并通过 [MAVLink Component Metadata service](https://mavlink.io/en/services/component_information.html) 提供给地面站。
这确保了元数据始终与机体上运行的代码保持同步。

该流程与 [事件元数据](../concept/events_interface.md#publishing-event-metadata-to-a-gcs) 的发布方式相同。
更多信息请参见 [PX4 Metadata (Translation & Publication)](../advanced/px4_metadata.md)

## 更多信息

- [查找/更新参数](../advanced_config/parameters.md)
- [参数参考](../advanced_config/parameter_reference.md)
- [Param实现](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/common/include/px4_platform_common/param.h#L129) (关于`.get()`、`.commit()`和其他方法的信息)