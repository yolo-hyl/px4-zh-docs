

# 参数和配置

PX4 使用 _参数子系统_（`float` 和 `int32_t` 值的平面表）和文本文件（用于启动脚本）来存储其配置。

本节详细讨论 _参数_ 子系统。  
它涵盖如何列出、保存和加载参数，以及如何定义参数并使其对地面站可用。

:::tip  
[System startup](../concept/system_startup.md) 和 [frame configuration](../dev_airframes/adding_a_new_frame.md) 启动脚本的工作方式在其他页面有详细说明。  
:::

## 命令行使用

PX4 [系统控制台](../debug/system_console.md) 提供了 [param](../modules/modules_command.md#param) 工具，可用于设置参数、读取其值、保存以及导出和恢复到文件中。

### 获取和设置参数

`param show` 命令可列出所有系统参数：

```sh
param show
```

通过通配符 "\*" 可以筛选部分参数名称：

```sh
nsh> param show RC_MAP_A*
Symbols: x = used, + = saved, * = unsaved
x   RC_MAP_AUX1 [359,498] : 0
x   RC_MAP_AUX2 [360,499] : 0
x   RC_MAP_AUX3 [361,500] : 0
x   RC_MAP_ACRO_SW [375,514] : 0

 723 parameters total, 532 used.
```

使用 `-c` 标志可显示所有已修改的参数（默认值以外的参数）：

```sh
param show -c
```

使用 `param show-for-airframe` 可仅显示当前机型定义文件（及其导入的默认值）中已修改的参数。

### 导出和加载参数

您可以保存任何已更改的参数（与机体默认值不同的参数）。

标准的 `param save` 命令会将这些参数存储到当前默认文件中：

```sh
param save
```

如果提供参数作为参数，它会将参数存储到新的位置：

```sh
param save /fs/microsd/vtol_param_backup
```

有两个不同的命令用于加载参数：

- `param load` 首先会将所有参数完全重置为默认值，然后用文件中保存的值覆盖参数。
- `param import` 仅用文件中的值覆盖参数值，然后保存结果（即实际上调用 `param save`）。

`load` 实际上会将参数重置为保存时的状态（我们说“实际上”，因为文件中保存的参数会被更新，但其他参数可能具有与创建参数文件时不同的固件定义的默认值）。

相比之下，`import` 会将文件中的参数与当前机体状态合并。
这可以用于例如仅导入包含校准数据的参数文件，而不会覆盖其余系统配置。

以下是两种情况的示例：

```sh
```

# 恢复保存文件时的参数
param load /fs/microsd/vtol_param_backup

# 可选地保存参数（加载时不会自动执行）
param save
```

```sh

```

# 将保存的参数与当前参数合并
param import /fs/microsd/vtol_param_backup

## 创建/定义参数

参数定义分为两个部分：

- [参数元数据](#参数元数据) 指定固件中每个参数的默认值，以及在地面站和文档中展示（和编辑）参数所需的元数据信息。
- [C/C++代码](#c-c-api) 提供从PX4模块和驱动程序中获取和/或订阅参数值的访问接口。

下面描述了编写元数据和代码的多种方法。  
在可行的情况下，代码应优先使用较新的[YAML元数据](#YAML元数据)和[C++ API](#C语言API)，而非旧版C参数/代码定义，因为前者更加灵活和稳健。

参数元数据被[编译进固件](#向地面站发布参数元数据)，  
并通过[MAVLink组件信息服务](https://mavlink.io/en/services/component_information.html)提供给地面站使用。

### 参数名称

参数名称不得超过16个ASCII字符。

按惯例，组内每个参数应共享相同的（有意义的）字符串前缀并接下划线，其中`MC_`和`FW_`用于特指多旋翼或固定翼系统的参数。此约定未强制执行。

名称必须在代码和[参数元数据](#参数元数据)中匹配，以正确关联参数及其元数据（包括固件中的默认值）。

### C / C++ API

PX4模块和驱动程序可以通过单独的C语言和C++ API访问参数值。

两个API之间的一个重要区别在于，C++版本具有更高效的标准化机制来同步参数值的变化（例如来自GCS）。

同步很重要，因为参数值可能在任何时刻被修改。
您的代码应始终使用参数存储中的当前值。
如果无法获取最新版本，则在参数修改后必须重新启动（通过`@reboot_required`元数据设置此要求）。

此外，C++版本在类型安全性和RAM开销方面表现更优。
缺点是编译时必须已知参数名称，而C API可以动态创建字符串作为名称。

#### C++ API

C++ API 提供宏来将参数声明为 _类属性_。  
你需要添加一些"模板代码"来定期监听与 _任意_ 参数更新相关的 [uORB主题](../middleware/uorb.md) 的变化。  
框架代码会（不可见地）处理跟踪影响你参数属性的 uORB 消息并保持同步。  
在其余代码中你可以直接使用定义的参数属性，它们将始终保持最新！

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

从 `ModuleParams` 派生你的类，并使用 `DEFINE_PARAMETERS` 指定参数列表及其关联的参数属性。  
参数名称必须与其参数元数据定义相同。

```cpp
class MyModule : ..., public ModuleParams
{
public:
	...

private:

	/**
	 * 检查参数变化并更新
	 */
	void parameters_update();

	DEFINE_PARAMETERS(
		(ParamInt<px4::params::SYS_AUTOSTART>) _sys_autostart,   /**< example parameter */
		(ParamFloat<px4::params::ATT_BIAS_MAX>) _att_bias_max  /**< another parameter */
	)

	// 订阅
	uORB::SubscriptionInterval _parameter_update_sub{ORB_ID(parameter_update), 1_s};

};
```

在 cpp 文件中添加模板代码以检查与参数更新相关的 uORB 消息。

在代码中定期调用 `parameters_update();` 以检查是否有更新：

```cpp
void Module::parameters_update()
{
	if (_parameter_update_sub.updated()) {
		parameter_update_s param_update;
		_parameter_update_sub.copy(&param_update);

		// 如果有任何参数更新，调用 updateParams() 检查
		// 该类属性是否需要更新（并执行更新）
		updateParams();
	}
}
```

在上述方法中：

- `_parameter_update_sub.updated()` 告知我们 `param_update` uORB 消息是否有任何更新（但不告知具体影响哪个参数）。
- 如果有"某些"参数更新，我们将更新复制到 `parameter_update_s` (`param_update`) 中，以清除待处理的更新。
- 然后调用 `ModuleParams::updateParams()`。
  这个方法会"在底层"更新我们 `DEFINE_PARAMETERS` 列表中所有参数属性。

这些参数属性（此处为 `_sys_autostart` 和 `_att_bias_max`）可以用来表示参数，并在参数值变化时自动更新。

:::tip
[应用程序/模块模板](../modules/module_template.md) 使用了新的 C++ API 但未包含 [参数元数据](#参数元数据)。
:::

#### C语言API

C语言API可以用于模块和驱动程序中。

首先包含参数API头文件：

```C
#include <parameters/param.h>
```

然后通过如下方式获取参数并赋值给变量（此处为`my_param`），以`PARAM_NAME`为例展示：
该变量`my_param`随后可在模块代码中使用。

```C
int32_t my_param = 0;
param_get(param_find("PARAM_NAME"), &my_param);
```

::: info
如果 `PARAM_NAME` 在参数元数据中被声明，那么其默认值将被设置，并且上述查找参数的调用应该总是成功。
:::

`param_find()` 是一个"代价高昂"的操作，它会返回一个句柄供 `param_get()` 使用。
如果需要多次读取参数，可以缓存该句柄并在需要时通过 `param_get()` 使用

```cpp
```

# 获取参数的句柄
param_t my_param_handle = PARAM_INVALID;
my_param_handle = param_find("PARAM_NAME");

# 在需要时查询参数的值
int32_t my_param = 0;
param_get(my_param_handle, &my_param);

### 参数元数据

PX4 使用一个广泛的参数元数据系统来驱动参数的用户界面展示，并在固件中为每个参数设置默认值。

:::tip
正确的元数据对地面站在用户体验方面至关重要。
:::

参数元数据可以存储在源代码树的任何位置，格式为 **.c** 或 **.yaml** 参数定义（YAML 定义为较新的格式，且更灵活）。通常它会与相关模块一起存储。

构建系统会通过 `make parameters_metadata` 提取元数据，用于构建 [参数参考](../advanced_config/parameter_reference.md) 以及 [地面站使用的参数信息](#向地面站发布参数元数据)。

:::warning
在添加新的参数文件后，应先执行 `make clean` 再进行构建以生成新参数（参数文件作为 _cmake_ 配置步骤的一部分被添加，该过程会在干净编译时触发，或者在修改 cmake 文件时触发）。
:::

#### YAML元数据

::: info  
在撰写本文时，YAML参数定义无法在_library_中使用。  
:::  

YAML元数据旨在完全替代**.c**定义。  
它支持所有相同的元数据，以及多实例定义等新功能。  

- YAML参数元数据模式在此：[validation/module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)。  
- YAML定义的使用示例可以在MAVLink参数定义中找到：[/src/modules/mavlink/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/module.yaml)。  
- 通过向模块的`CMakeLists.txt`文件的`px4_add_module`部分添加  

  ```cmake  
  MODULE_CONFIG  
  	module.yaml  
  ```  

  来在cmake构建系统中注册YAML文件。

#### 多实例（模板化）YAML元数据

模板化参数定义在[YAML参数定义](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)中受到支持（模板化参数代码不支持）。

YAML允许您通过 `${i}` 在参数名称、描述等中定义实例编号。
例如，以下内容将生成 MY_PARAM_1_RATE、MY_PARAM_2_RATE 等。

```yaml
MY_PARAM_${i}_RATE:
  description:
    short: Maximum rate for instance ${i}
```

以下 YAML 定义提供了起始和结束索引的配置：

- `num_instances`（默认值 1）：生成的实例数量（>=1）
- `instance_start`（默认值 0）：第一个实例编号。如果为 0，`${i}` 会扩展为 [0, N-1]。

完整示例请参见 MAVLink 参数定义：[/src/modules/mavlink/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/module.yaml)

#### c 参数元数据

定义参数元数据的传统方法是使用扩展名为 **.c** 的文件（截至撰写时，这是源代码树中最常用的方法）。

参数元数据部分的示例如下：

```cpp
/**
 * 俯仰P增益
 *
 * 俯仰比例增益，即对于1弧度的误差，期望的角速度（单位：rad/s）。
 *
 * @unit 1/s
 * @min 0.0
 * @max 10
 * @decimal 2
 * @increment 0.0005
 * @reboot_required true
 * @group Multicopter Attitude Control
 */
PARAM_DEFINE_FLOAT(MC_PITCH_P, 6.5f);
```

```cpp
/**
 * 基于GPS速度的加速度补偿。
 *
 * @group Attitude Q estimator
 * @boolean
 */
PARAM_DEFINE_INT32(ATT_ACC_COMP, 1);
```

末尾的 `PARAM_DEFINE_*` 宏定义指定了参数类型（`PARAM_DEFINE_FLOAT` 或 `PARAM_DEFINE_INT32`）、参数名称（必须与代码中使用的名称一致）以及固件中的默认值。

注释块中的所有行都是可选的，主要用于控制地面站在显示和编辑参数时的选项。每行的具体作用如下（更多细节请参见 [module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)）：

```cpp
/**
 * <标题>
 *
 * <更详细的描述，可跨多行>
 *
 * @unit <单位，例如m表示米>
 * @min <最小合理值。可由用户覆盖>
 * @max <最大合理值。可由用户覆盖>
 * @decimal <小数位数。可由用户覆盖>
 * @increment <此参数在UI中递增的"步长">
 * @reboot_required true <如果修改此参数需要系统重启，请添加此项>
 * @boolean <如果整型参数表示布尔值，请添加此项>
 * @group <为形成组的参数指定标题>
 */
```

## 向地面站发布参数元数据

参数元数据 JSON 文件被编译到固件中（或托管在互联网上），并通过 [MAVLink 组件元数据服务](https://mavlink.io/en/services/component_information.html) 提供给地面站。
这确保了元数据始终与机体上运行的代码保持同步。

此流程与 [事件元数据](../concept/events_interface.md#publishing-event-metadata-to-a-gcs) 的处理方式相同。
如需更多信息，请参阅 [PX4 元数据 (翻译与发布)](../advanced/px4_metadata.md)

## 更多信息

- [查找/更新参数](../advanced_config/parameters.md)
- [参数参考](../advanced_config/parameter_reference.md)
- [Param实现](https://github.com/PX4/PX4-Autopilot/blob/main/platforms/common/include/px4_platform_common/param.h#L129) (关于`.get()`、`.commit()`和其他方法的信息)