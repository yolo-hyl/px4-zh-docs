

# DShot电调

DShot是ESC协议的另一种选择，相较于[PWM](../peripherals/pwm_escs_and_servo.md)或[OneShot](../peripherals/oneshot.md)具有以下优势：

- 延迟更低。
- 通过校验和提升稳定性。
- 无需ESC校准，因为协议使用数字编码。
- 部分电调支持/提供遥测反馈。
- 可通过指令在需要时反转电机旋转方向（无需物理上移动线缆/重新焊接）。
- 支持其他有用的指令。

本主题介绍如何连接和配置DShot电调。

## 接线/连接 {#wiring}

DShot电调的接线方式与[PWM电调](pwm_escs_and_servo.md)相同。唯一的区别是它们只能连接到FMU，且通常只能连接到部分引脚。

::: info
在接线之前，建议查看执行器配置界面，确认控制器上哪些引脚可用于DShot。
:::

同时配备FMU和IO板的Pixhawk控制器通常将它们标记为`AUX`（FMU）和`MAIN`（IO）。这些分别对应执行器配置界面中的`PWM AUX`和`PWM MAIN`输出标签。对于这类控制器，应将DShot电调连接到`AUX`端口。

没有IO板的控制器通常将（唯一）输出端口标记为`MAIN`，DShot电调应连接在此处。如果控制器没有IO板且具备专用固件，则执行器分配将对应`PWM MAIN`输出。但如果相同固件被用于有/无IO板的硬件（如Pixhawk 4和Pixhawk 4 Mini），则执行器分配标签始终相同：`PWM AUX`（即在"mini"型号中不匹配端口标签`MAIN`）。

## 配置

:::warning  
在更改电调配置参数前请移除螺旋桨！  
:::

在[执行器配置](../config/actuators.md)中为所需的输出启用DShot。

DShot提供了不同的速度选项：_DShot150_、_DShot300_ 和 _DShot600_，其中的数字表示速度（单位为千比特/秒）。  
应将参数设置为电调支持的最高速度（根据其数据手册）。

然后连接电池并启动机体。  
电调应初始化，电机应按正确方向旋转。

- 如果电机旋转方向与[选定的机型](../airframes/airframe_reference.md)不匹配，可以通过UI中的 **Set Spin Direction** 选项进行反转（在选择DShot并分配电机后此选项将出现）。  
  也可通过发送[ESC Command](#commands)实现电机反转。

## 电调命令 {#commands}

命令可以通过 [MAVLink shell](../debug/mavlink_shell.md) 发送给电调。  
关于支持的命令的完整参考，请参见 [这里](../modules/modules_driver.md#dshot)。

最重要的命令包括：

- 使连接到FMU输出引脚1的电机发出蜂鸣声（有助于识别电机）

  ```sh
  dshot beep1 -m 1
  ```

- 检索电调信息（需要遥测，参见下文）：

  ```sh
  nsh> dshot esc_info -m 2
  INFO  [dshot] ESC Type: #TEKKO32_4in1#
  INFO  [dshot] MCU Serial Number: xxxxxx-xxxxxx-xxxxxx-xxxxxx
  INFO  [dshot] Firmware version: 32.60
  INFO  [dshot] Rotation Direction: normal
  INFO  [dshot] 3D Mode: off
  INFO  [dshot] Low voltage Limit: off
  INFO  [dshot] Current Limit: off
  INFO  [dshot] LED 0: unsupported
  INFO  [dshot] LED 1: unsupported
  INFO  [dshot] LED 2: unsupported
  INFO  [dshot] LED 3: unsupported
  ```

- 永久设置连接到FMU输出引脚1的电机旋转方向（当电机_不_旋转时）：

  - 设置旋转方向为 `reversed`：

    ```sh
    dshot reverse -m 1
    dshot save -m 1
    ```

    检索电调信息将显示：

    ```sh
    Rotation Direction: reversed
    ```

  - 设置旋转方向为 `normal`：

    ```sh
    dshot normal -m 1
    dshot save -m 1
    ```

    检索电调信息将显示：

    ```sh
    Rotation Direction: normal
    ```

  ::: info

  - 如果电机正在旋转，或者电调已经设置为对应的方向，这些命令将没有效果。
  - 如果在更改方向后没有调用`save`，电调在重启后会恢复到最后保存的方向（normal或reversed）。

  :::

## ESC遥测

部分ESC能够通过UART RX端口将遥测信息发送回飞控。  
这些DShot ESC会有额外的遥测线。

提供的遥测信息包括：

- 温度  
- 电压  
- 电流  
- 累计电流消耗  
- 转速值  

要启用此功能（需ESC支持）：

1. 将所有ESC的遥测线并联，然后连接到未使用飞控串口的RX引脚。  
1. 通过[DSHOT_TEL_CFG](../advanced_config/parameter_reference.md#DSHOT_TEL_CFG)在该串口启用遥测功能。  

重启后可通过以下命令检查遥测是否生效（需确保电池已连接）：

```sh
dshot esc_info -m 1
```

:::tip
可能需要配置[MOT_POLE_COUNT](../advanced_config/parameter_reference.md#MOT_POLE_COUNT)才能获取正确的转速值。
:::

:::tip
并非所有支持DShot的ESC都支持[esc_info]（例如APD 80F3x），即使遥测已启用。  
报错信息为：

```sh
ERROR [dshot] No data received. If telemetry is setup correctly, try again.
```

请查阅厂商文档确认详细信息。
:::

## 双向DShot（遥测）

<Badge type="tip" text="PX4 v1.16" />

双向DShot是一种协议，可通过单根线缆提供包括高速率电调RPM数据、电压、电流和温度在内的遥测信息。

PX4当前的实现仅支持以高频率从每个电调中收集eRPM数据。该遥测数据显著提升了[动态陷波滤波器](../config_mc/filter_tuning.md#dynamic-notch-filters)的性能，并使机体调校更加精确。

::: info
如果需要电压、电流或温度信息，上述[电调遥测](#ESC遥测)目前仍然是必需的。
其设置和使用与双向DShot无关。
:::

### 硬件设置

ESC必须连接到FMU的输出端口。  
在只有一个PWM总线的飞控上，这些输出端口会被标记为`MAIN`；而在同时具备`MAIN`和`AUX`端口的飞控上（即带有I/O板的飞控），则标记为`AUX`。

:::warning **硬件支持有限**  
此功能仅支持以下处理器的飞控：  

- STM32H7: 前四个FMU输出  
  - 必须连接到前4个FMU输出，且这些输出需映射到同一个定时器。  
  - [KakuteH7](../flight_controller/kakuteh7v2.md) 不被支持，因为输出未映射到同一定时器。  
- [i.MXRT](../flight_controller/nxp_mr_vmu_rt1176.md) (V6X-RT & Tropic): 8个FMU输出  

其他板型均不支持。  
:::

### 配置 {#bidirectional-dshot-configuration}

启用双向DShot功能时，请设置[DSHOT_BIDIR_EN](../advanced_config/parameter_reference.md#DSHOT_BIDIR_EN)参数。

系统通过[MOT_POLE_COUNT](../advanced_config/parameter_reference.md#MOT_POLE_COUNT)参数，将接收到的eRPM数据转换为实际电机RPM值。
该参数必须正确设置才能确保RPM报告的准确性。