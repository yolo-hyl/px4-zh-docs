# PX4 元数据

PX4 使用和生成带有可读性元数据的数据，这些数据分为人类可读和机器可读两种形式：

- [参数](../advanced_config/parameters.md) 用于配置 PX4 行为。
  - 参数通过 ID 字符串表示，该字符串映射到 PX4 中存储的值。
  - 关联的元数据包括设置描述、可能的取值范围，以及值的展示方式信息（例如位掩码）。
- [事件](../concept/events_interface.md) 用于通知事件，例如失联原因、低电量警告、校准完成等。
  - 事件通过 ID 表示，并附带日志级别和参数。
  - 关联的元数据包含消息、描述以及事件参数列表（包括参数类型）。
- [执行器](../config/actuators.md) 配置用于自定义机体几何结构，分配执行器和电机到飞控输出，并测试执行器和电机响应。
  - 元数据包含支持的机体几何结构信息、输出驱动列表及其配置方式。
  - _QGroundControl_ 使用这些信息动态构建配置界面。

元数据和元数据翻译与外部系统（如 QGroundControl）共享，使其能够显示参数和事件信息，并配置机体几何结构和执行器输出映射。

本主题将解释如何定义元数据以及协助翻译字符串（同时简要说明其工作原理）。

## 元数据翻译

PX4 元数据的翻译通过 Crowdin 在 [PX4-Metadata-Translations](https://crowdin.com/project/px4-metadata-translations) 项目中进行。
关于如何使用 PX4 和 Crowdin 进行翻译的更多信息，请参见 [Translation](../contribute/translation.md)。

## 定义元数据

PX4 元数据在 PX4 源代码中与其关联数据一同定义。
这可以通过 C/C++ 注释中的特殊标记来定义元数据字段和值，或使用 YAML 文件实现。

更多信息请参见各数据类型的专题：

- [参数与配置 > 创建/定义参数](../advanced/parameters_and_configurations.md#creating-defining-parameters)
- [事件接口](../concept/events_interface.md)
- [执行器元数据](#执行器元数据)（下文）

## 元数据工具链

所有元数据类型的处理流程相同。

每次构建 PX4 时，元数据都会被收集到 JSON 文件中。

对于大多数飞控器（因其具有足够的 FLASH 空间），JSON 文件会被 xz 压缩并存储在生成的二进制文件中。
该文件通过 MAVLink [组件元数据协议](https://mavlink.io/en/services/component_information.html) 共享给地面站。
使用组件元数据协议可确保接收方始终能获取到运行在机体上的代码的最新元数据。
事件元数据还会添加到日志文件中，允许日志分析工具（如 Flight Review）使用正确的元数据显示事件。

对于内存受限的飞控器目标，二进制文件不会存储参数元数据，而是引用存储在 `px4-travis.s3.amazonaws.com` 的相同数据。
例如，这适用于 [Omnibus F4 SD](../flight_controller/omnibus_f4_sd.md)。
元数据通过 [github CI](https://github.com/PX4/PX4-Autopilot/blob/main/.github/workflows/metadata.yml) 上传到所有构建目标（因此参数合并到主分支后才会可用）。

::: info
可以通过检查 [px4board 定义文件](https://github.com/PX4/PX4-Autopilot/blob/main/boards/omnibus/f4sd/default.px4board) 中是否包含 `CONFIG_BOARD_CONSTRAINED_FLASH=y` 来识别内存受限的板卡。

如果在 FLASH 受限的板卡上进行自定义开发，可以通过调整 [此处](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/component_information/CMakeLists.txt#L41) 的 URL 指向其他服务器。
:::

当机体中没有参数元数据时，`px4-travis.s3.amazonaws.com` 上的元数据将被使用。
它也可能作为后备方案，以避免通过低速遥测链路下载时的缓慢速度。

`main` 分支的 CI 构建的元数据 JSON 文件还会被复制到 GitHub 仓库：[PX4/PX4-Metadata-Translations](https://github.com/PX4/PX4-Metadata-Translations/)。
这与 Crowdin 集成以获取翻译，翻译结果以 xz 压缩文件形式存储在 [translated](https://github.com/PX4/PX4-Metadata-Translations/tree/main/translated) 文件夹中，每个语言对应一个翻译文件。
这些文件由机体组件元数据引用，并在需要时下载。
更多信息请参见 [PX4-Metadata-Translations](https://github.com/PX4/PX4-Metadata-Translations/) 和 [组件元数据协议 > 翻译](https://mavlink.io/en/services/component_information.html#translation)。

::: info
主分支的参数 XML 文件通过 CI 复制到 QGC 源代码树中，并作为后备方案在组件元数据协议不可用时使用（这种方法早于组件元数据协议的存在）。
:::

### 执行器元数据

下图展示了执行器元数据如何从源代码中组装并被 QGroundControl 使用：

![执行器元数据](../../assets/diagrams/actuator_metadata_processing.svg)
<!-- 来源: https://docs.google.com/drawings/d/1hMQmIijdFjr21rREcXj50qz0C1b47JW0OEa6p5P231k/edit -->

- **左侧**：元数据在不同模块的 `module.yml` 文件中定义。
  `control_allocator` 模块定义了几何结构，而每个输出驱动定义了其通道集和配置参数。
  [schema 文件](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml) 文档记录了这些 YAML 文件的结构。
- **中间**：构建时，当前目标启用的所有模块的 `module.yml` 文件将被解析，并通过 [Tools/module_config/generate_actuators_metadata.py](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/module_config/generate_actuators_metadata.py) 脚本转换为 `actuators.json` 文件。
  该文件也有对应的 [schema 文件](https://github.com/mavlink/mavlink/blob/master/component_metadata/actuators.schema.json)。
- **右侧**：运行时，QGroundControl 通过 MAVLink 组件元数据 API 请求 JSON 文件（如上所述）。

## 更多信息

- [参数与配置](../advanced/parameters_and_configurations.md)
- [事件接口](../concept/events_interface.md)
- [翻译](../contribute/translation.md)
- [组件元数据协议](https://mavlink.io/en/services/component_information.html) (mavlink.io)
- [PX4-Metadata-Translations](https://github.com/PX4/PX4-Metadata-Translations/) (Github)