

# 系统范围回放

基于 ORB 消息可以记录和回放系统中任意部分的操作。

回放功能可用于基于真实数据测试不同参数值的影响，比较不同估计器等。

## 先决条件

第一步是确定需要重放的模块或模块集合。  
然后，确定这些模块的所有输入（即订阅的ORB主题）。  
对于系统级重放，这包括所有硬件输入：传感器、遥控输入、MAVLink命令和文件系统。  

所有确定的主题需要以全速率记录（参见[logging](../dev_log/logging.md)）。  
对于`ekf2`，默认记录的主题集已经满足这一要求。  

所有重放的主题必须仅包含一个绝对时间戳，该时间戳是自动生成的字段`timestamp`。  
如果有其他时间戳，它们必须相对于主时间戳。  
示例请参见[SensorCombined.msg](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorCombined.msg)。  
原因如下。

## 使用

- 首先选择要回放的文件并构建目标（在PX4-Autopilot目录内执行）：

  ```sh
  export replay=<absolute_path_to_log_file.ulg>
  make px4_sitl_default
  ```

  这将在单独的构建目录`build/px4_sitl_default_replay`中生成构建/编译输出（避免参数与常规构建冲突）。
  由于构建系统通过`replay`环境变量知道处于回放模式，因此可以选择任何posix SITL构建目标进行回放。

- 在文件`build/px4_sitl_default_replay/rootfs/orb_publisher.rules`中添加ORB发布者规则。
  该文件定义了允许发布特定消息的模块。
  其格式如下：

  ```sh
  restrict_topics: <topic1>, <topic2>, ..., <topicN>
  module: <module>
  ignore_others: <true/false>
  ```

  这表示给定的主题列表只能由`<module>`（即命令名）发布。
  其他模块向这些主题发布的消息将被静默忽略。
  如果`ignore_others`为`true`，则`<module>`向其他主题发布的消息也会被忽略。

  对于回放，我们只希望`replay`模块能够发布之前确定的主题列表。
  因此，回放`ekf2`时规则文件应如下所示：

  ```sh
  restrict_topics: sensor_combined, vehicle_gps_position, vehicle_land_detected
  module: replay
  ignore_others: true
  ```

  这样，通常负责发布这些主题的模块在回放时无需禁用。

- _(可选)_ 设置参数覆盖（参见[下方说明](#在原始日志中覆盖参数)）。
- _(可选)_ 从SD卡复制`dataman`任务文件到构建目录。
  仅当需要回放任务时才需要此操作。
- 启动回放：

  ```sh
  make px4_sitl_default jmavsim
  ```

  此命令将自动打开日志文件，应用参数并启动回放。
  完成后会报告结果并退出。
  新生成的日志文件可在`rootfs/fs/microsd/log`中分析，按日期组织的子目录中存储。
  回放生成的日志文件名将带有`_replayed`后缀。

  注意：上述命令会显示模拟器界面，但根据回放内容，可能不会显示实际运行情况。
  仍可通过QGC连接，例如在回放期间查看姿态变化。

- 最后，清除环境变量以恢复正常使用构建目标：

  ```sh
  unset replay
  ```

### 在原始日志中覆盖参数

默认情况下，回放过程中会应用原始日志文件中的所有参数。
如果在记录期间参数发生变化，回放时会在相应时间点进行更改。

参数可以通过两种方式在回放过程中被覆盖：_固定_ 和 _动态_。
当参数被覆盖时，回放过程中不会应用日志中对应的参数更改。

- **固定参数覆盖** 会从回放开始时覆盖参数。
  它们在文件 `build/px4_sitl_default_replay/rootfs/replay_params.txt` 中定义，每行应采用 `<param_name> <value>` 格式。
  例如：

  ```sh
  EKF2_RNG_NOISE 0.1
  ```

- **动态参数覆盖** 会在指定时间更新参数值。
  这些参数仍会根据日志或固定覆盖的值初始化。
  参数更新事件应在 `build/px4_sitl_default_replay/rootfs/replay_params_dynamic.txt` 中定义，每行采用 `<param_name> <value> <timestamp>` 格式。
  时间戳表示从日志开始的秒数。例如：

  ```sh
  EKF2_RNG_NOISE 0.15 23.4
  EKF2_RNG_NOISE 0.05 56.7
  EKF2_RNG_DELAY 4.5 30.0
  ```

### 重要说明

- 在回放期间，日志文件中的所有数据丢失都会被报告。  
  这些数据丢失会对回放产生负面影响，因此在记录过程中应避免数据丢失。  
- 目前只能以"实时"速度进行回放：与录制时速度一致。  
  计划在未来进行扩展。  
- 时间戳为0的消息将被视为无效且不会被回放。

## EKF2 回放

这是针对快速EKF2回放的系统级回放专用版本。

::: info
[多个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-multiple-ekf-instances)的飞行日志记录和回放不支持。
要启用EKF回放记录，必须设置参数以启用[单个EKF2实例](../advanced_config/tuning_the_ecl_ekf.md#running-a-single-ekf-instance)。
:::

在EKF2模式下，回放会自动创建上述描述的ORB发布规则。

执行EKF2回放的步骤：

- 使用 `SDLOG_MODE` 设置为 `1` 记录原始日志，以从启动开始记录。

- 除了 `replay` 环境变量，还需设置 `replay_mode` 为 `ekf2`：

  ```sh
  export replay_mode=ekf2
  export replay=<absolute_path_to_log.ulg>
  ```

- 使用 `none` 目标运行回放：

  ```sh
  make px4_sitl none
  ```

- 完成后，清除 `replay` 和 `replay_mode` 变量：

  ```sh
  unset replay; unset replay_mode
  ```

### 调整用于回放的EKF2特定参数

首先安装 `pyulog`：

```sh
pip install --user pyulog
```

将原始日志的参数提取到 `replay_params.txt`：

```sh
ulog_params -i "$replay" -d ' ' | grep -e '^EKF2' > build/px4_sitl_default_replay/rootfs/replay_params.txt
```

根据需要调整这些参数，必要时可在 `replay_params_dynamic.txt` 中添加动态参数覆盖。

## 幕后机制

Replay 被分为三个组件：

- 一个 replay 模块  
  这些因素会对 replay 产生负面影响，因此在录制过程中应谨慎避免断开连接。
- 目前仅能以 "实时" 模式回放：回放速度与录制时的速度相同。

replay 模块读取日志文件，并以与录制时相同的速度发布消息。  
每个消息的时间戳都会添加一个固定偏移量以匹配当前系统时间（这就是为什么所有其他时间戳都需要是相对的）。

命令 `replay tryapplyparams` 会在所有其他模块加载前执行，用于应用日志中的参数和用户设置的参数。  
随后作为最后一步，命令 `replay trystart` 会再次应用参数并启动实际回放。  
如果环境变量 `replay` 未设置，这两个命令将不会执行任何操作。

如上所述，**ORB 发布者规则**允许选择回放系统的哪一部分。它们仅针对 posix SITL 目标进行编译。

**时间处理**仍然是一个 **open point**，需要进一步实现。