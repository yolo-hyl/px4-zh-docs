# 系统级重放

可以通过ORB消息记录和重放系统中任意部分。

重放可用于基于真实数据测试不同参数值的影响、比较不同估计算法等。

## 前提条件

第一步是确定需要重放的模块或模块组合。
然后识别这些模块的所有输入，即订阅的ORB主题。
系统级重放需要所有硬件输入：传感器、遥控输入、MAVLink命令和文件系统。

所有识别的主题需要以全速率记录（参见[日志记录](../dev_log/logging.md)）。
对于`ekf2`，默认记录的主题集已经满足此要求。

重要的是所有重放的主题仅包含一个绝对时间戳，即自动生成的字段`timestamp`。
如果存在多个时间戳，它们必须相对于主时间戳。
示例参见[SensorCombined.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorCombined.msg)。
原因如下所述。

## 使用方法

- 首先选择要重放的文件并构建目标（在PX4-Autopilot目录中）：

  ```sh
  export replay=<绝对路径到日志文件.ulg>
  make px4_sitl_default
  ```

  这将在单独的构建目录`build/px4_sitl_default_replay`中生成构建/make输出（因此参数不会与常规构建冲突）。
  可以为重放选择任何posix SITL构建目标，因为构建系统通过`replay`环境变量知道处于重放模式。

- 在文件`build/px4_sitl_default_replay/rootfs/orb_publisher.rules`中添加ORB发布规则。
  该文件定义了允许发布特定消息的模块。
  其格式如下：

  ```sh
  restrict_topics: <topic1>, <topic2>, ..., <topicN>
  module: <module>
  ignore_others: <true/false>
  ```

  这意味着给定的主题列表只能由`<module>`（命令名称）发布。
  其他模块发布的这些主题将被静默忽略。
  如果`ignore_others`为`true`，`<module>`发布的其他主题将被忽略。

  对于重放，我们只需要`replay`模块能够发布之前识别的主题列表。
  因此，重放`ekf2`时规则文件应如下所示：

  ```sh
  restrict_topics: sensor_combined, vehicle_gps_position, vehicle_land_detected
  module: replay
  ignore_others: true
  ```

  这样，通常发布这些主题的模块不需要在重放时禁用。

- _(可选)_ 设置参数覆盖（参见[下方说明](#overriding-parameters-in-the-original-log))。
- _(可选)_ 将SD卡中的`dataman`任务文件复制到构建目录。
  如果需要重放任务，这一步是必需的。
- 启动重放：

  ```sh
  make px4_sitl_default jmavsim
  ```

  这将自动打开日志文件，应用参数并开始重放。
  完成后将报告结果并退出。
  新生成的日志文件可进行分析，位于`rootfs/fs/microsd/log`中，按日期组织的子目录下。
  重放日志文件名将带有`_replayed`后缀。

  注意上述命令会显示模拟器，但根据重放内容，可能不会显示实际发生的情况。
  仍可通过QGC连接，例如在重放期间查看姿态变化。

- 最后取消环境变量，以便再次使用正常构建目标：

  ```sh
  unset replay
  ```

### 在原始日志中覆盖参数

默认情况下，重放期间会应用原始日志文件中的所有参数。
如果在记录期间参数发生变化，重放时会在正确时间改变参数。

参数在重放期间可以以两种方式覆盖：_固定_和_动态_。
当参数被覆盖时，日志中的相应参数变化在重放期间不会被应用。

- **固定参数覆盖**将从重放开始时覆盖参数。
  它们在文件`build/px4_sitl_default_replay/rootfs/replay_params.txt`中定义，每行格式为`<param_name> <value>`。
  例如：

  ```sh
  EKF2_RNG_NOISE 0.1
  ```

- **动态参数覆盖**将在指定时间更新参数值。
  这些参数仍会初始化为日志中的值或固定覆盖值。
  参数更新事件应在`build/px4_sitl_default_replay/rootfs/replay_params_dynamic.txt`中定义，每行格式为`<param_name> <value> <timestamp>`。
  时间戳是日志开始后的秒数。例如：

  ```sh
  EKF2_RNG_NOISE 0.15 23.4
  EKF2_RNG_NOISE 0.05 56.7
  EKF2_RNG_DELAY 4.5 30.0
  ```

### 重要说明

- 重放期间，日志文件中的所有掉帧都会被报告。
  这会对重放产生负面影响，因此在记录时需注意避免掉帧。
- 目前仅支持以'实时'模式重放：与记录速度相同。
  未来计划扩展此功能。
- 时间戳为0的消息将被视为无效且不被重放。

## EKF2重放

这是系统级重放的特化版本，用于快速EKF2重放。

::: info
不支持包含[多个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-multiple-ekf-instances)的飞行日志的记录和重放。
启用EKF重放记录前，必须将参数设置为启用[单个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-multiple-ekf-instances)。
:::

在EKF2重放的特定部分，需要确保参数覆盖的方法和步骤清晰明了。例如，“fixed parameter overrides”和“dynamic parameter overrides”需要准确翻译，同时保持术语的一致性。此外，代码示例如`pip install --user pyulog`需要保留原样，确保用户可以正确执行。

## 系统级重放

可以通过ORB消息记录和重放系统中任意部分。

重放可用于基于真实数据测试不同参数值的影响、比较不同估计算法等。

## 前提条件

第一步是确定需要重放的模块或模块组合。
然后识别这些模块的所有输入，即订阅的ORB主题。
系统级重放需要所有硬件输入：传感器、遥控输入、MAVLink命令和文件系统。

所有识别的主题需要以全速率记录（参见[日志记录](../dev_log/logging.md)）。
对于`ekf2`，默认记录的主题集已经满足此要求。

重要的是所有重放的主题仅包含一个绝对时间戳，即自动生成的字段`timestamp`。
如果存在多个时间戳，它们必须相对于主时间戳。
示例参见[SensorCombined.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorCombined.msg)。
原因如下所述。

## 使用方法

- 首先选择要重放的文件并构建目标（在PX4-Autopilot目录中）：

  ```sh
  export replay=<绝对路径到日志文件.ulg>
  make px4_sitl_default
  ```

  这将在单独的构建目录`build/px4_sitl_default_replay`中生成构建/make输出（因此参数不会与常规构建冲突）。
  可以为重放选择任何posix SITL构建目标，因为构建系统通过`replay`环境变量知道处于重放模式。

- 在文件`build/px4_sitl_default_replay/rootfs/orb_publisher.rules`中添加ORB发布规则。
  该文件定义了允许发布特定消息的模块。
  其格式如下：

  ```sh
  restrict_topics: <topic1>, <topic2>, ..., <topicN>
  module: <module>
  ignore_others: <true/false>
  ```

  这意味着给定的主题列表只能由`<module>`（命令名称）发布。
  其他模块发布的这些主题将被静默忽略。
  如果`ignore_others`为`true`，`<module>`发布的其他主题将被忽略。

  对于重放，我们只需要`replay`模块能够发布之前识别的主题列表。
  因此，重放`ekf2`时规则文件应如下所示：

  ```sh
  restrict_topics: sensor_combined, vehicle_gps_position, vehicle_land_detected
  module: replay
  ignore_others: true
  ```

  这样，通常发布这些主题的模块不需要在重放时禁用。

- _(可选)_ 设置参数覆盖（参见[下方说明](#overriding-parameters-in-the-original-log))。
- _(可选)_ 将SD卡中的`dataman`任务文件复制到构建目录。
  如果需要重放任务，这一步是必需的。
- 启动重放：

  ```sh
  make px4_sitl_default jmavsim
  ```

  这将自动打开日志文件，应用参数并开始重放。
  完成后将报告结果并退出。
  新生成的日志文件可进行分析，位于`rootfs/fs/microsd/log`中，按日期组织的子目录下。
  重放日志文件名将带有`_replayed`后缀。

  注意上述命令会显示模拟器，但根据重放内容，可能不会显示实际发生的情况。
  仍可通过QGC连接，例如在重放期间查看姿态变化。

- 最后取消环境变量，以便再次使用正常构建目标：

  ```sh
  unset replay
  ```

### 在原始日志中覆盖参数

默认情况下，重放期间会应用原始日志文件中的所有参数。
如果在记录期间参数发生变化，重放时会在正确时间改变参数。

参数在重放期间可以以两种方式覆盖：_固定_和_动态_。
当参数被覆盖时，日志中的相应参数变化在重放期间不会被应用。

- **固定参数覆盖**将从重放开始时覆盖参数。
  它们在文件`build/px4_sitl_default_replay/rootfs/replay_params.txt`中定义，每行格式为`<param_name> <value>`。
  例如：

  ```sh
  EKF2_RNG_NOISE 0.1
  ```

- **动态参数覆盖**将在指定时间更新参数值。
  这些参数仍会初始化为日志中的值或固定覆盖值。
  参数更新事件应在`build/px4_sitl_default_replay/rootfs/replay_params_dynamic.txt`中定义，每行格式为`<param_name> <value> <timestamp>`。
  时间戳是日志开始后的秒数。例如：

  ```sh
  EKF2_RNG_NOISE 0.15 23.4
  EKF2_RNG_NOISE 0.05 56.7
  EKF2_RNG_DELAY 4.5 30.0
  ```

### 重要说明

- 重放期间，日志文件中的所有掉帧都会被报告。
  这会对重放产生负面影响，因此在记录时需注意避免掉帧。
- 目前仅支持以'实时'模式重放：与记录速度相同。
  未来计划扩展此功能。
- 时间戳为0的消息将被视为无效且不被重放。

## EKF2重放

这是系统级重放的特化版本，用于快速EKF2重放。

::: info
不支持包含[多个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-multiple-ekf-instances)的飞行日志的记录和重放。
启用EKF重放记录前，必须将参数设置为启用[单个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-multiple-ekf-instances)。
:::

## 时间处理

时间处理仍然是一个**待解决的问题**，需要实现。