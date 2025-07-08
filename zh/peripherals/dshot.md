# DShot电调

DShot是一种替代电调协议，相较于[PWM](../peripherals/pwm_escs_and_servo.md)或[OneShot](../peripherals/oneshot.md)具有以下优势：

- 降低延迟
- 通过校验和提高抗干扰能力
- 无需电调校准（协议使用数字编码）
- 部分电调支持遥测反馈
- 可通过命令反转电机旋转方向（无需物理调线/重新焊接）
- 支持其他有用的命令

本主题展示如何连接和配置DShot电调。

## 布线/连接 {#wiring}

DShot电调的接线方式与[PWM电调](pwm_escs_and_servo.md)相同。唯一区别是它们只能连接到FMU，且通常只能连接到部分引脚。

::: info
在布线前，请检查执行器配置界面查看控制器上哪些引脚支持DShot！
:::

具有FMU和IO板的Pixhawk控制器通常分别标记为`AUX`（FMU）和`MAIN`（IO）。这些与执行器配置界面的`PWM AUX`和`PWM MAIN`输出选项卡对应。对于此类控制器，请将DShot电调连接到`AUX`端口。

没有IO板的控制器通常将（单个）输出端口标记为`MAIN`，DShot电调应连接到此处。如果控制器没有IO板但自带固件，执行器分配将对应`PWM MAIN`输出。然而对于硬件设计兼容带/不带IO板的控制器（如Pixhawk 4和Pixhawk 4 Mini），执行器分配选项始终使用`PWM AUX`（即使端口标记为`MAIN`）。

## 配置

:::warning
更改电调配置参数前请移除螺旋桨！
:::

在[执行器配置](../config/actuators.md)中为所需输出启用DShot。

DShot支持不同速率选项：_DShot150_、_DShot300_和_DShot600_，数字表示千位/秒速率。应将参数设置为电调支持的最高速率（参考其数据手册）。

然后连接电池并解锁机体。电调应初始化且电机按正确方向旋转。

- 如果电机旋转方向与[选定机体](../airframes/airframe_reference.md)不匹配，可通过UI的**Set Spin Direction**选项反转（该选项在选择DShot并分配电机后出现）。也可通过[ESC命令](#commands)反转电机。

## ESC命令 {#commands}

可通过[MAVLink shell](../debug/mavlink_shell.md)向电调发送命令。完整支持命令参考见[此处](../modules/modules_driver.md#dshot)。

最重要的命令包括：

- 使连接到FMU输出引脚1的电机发出蜂鸣声（有助于识别电机）

  ```sh
  dshot beep1 -m 1
  ```

- 获取电调信息（需要遥测，见下文）：

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

- 永久设置连接到FMU输出引脚1的电机旋转方向（电机需停止旋转）：

  - 设置为`reversed`方向：

    ```sh
    dshot reverse -m 1
    dshot save -m 1
    ```

    获取电调信息将显示：

    ```sh
    Rotation Direction: reversed
    ```

  - 设置为`normal`方向：

    ```sh
    dshot normal -m 1
    dshot save -m 1
    ```

    获取电调信息将显示：

    ```sh
    Rotation Direction: normal
    ```

  ::: info

  - 电机旋转中或电调已设置为对应方向时命令无效。
  - 若未在更改方向后调用`save`，重启后电调将恢复为上次保存的方向（normal或reversed）。

  :::

## 电调遥测

部分电调可通过UART RX端口向飞控发送遥测数据。这些DShot电调会有额外的遥测线。

提供的遥测数据包括：

- 温度
- 电压
- 电流
- 累计电流消耗
- 转速值

在支持的电调上启用此功能：

1. 将所有电调的遥测线连接后，接至飞控串口的空闲RX引脚。
1. 使用[DSHOT_TEL_CFG](../advanced_config/parameter_reference.md#DSHOT_TEL_CFG)在该串口启用遥测。

重启后可通过以下命令验证遥测是否工作（确保电池已连接）：

```sh
dshot esc_info -m 1
```

:::tip
可能需要配置[MOT_POLE_COUNT](../advanced_config/parameter_reference.md#MOT_POLE_COUNT)以获得准确的转速报告。
:::

## 双向DShot

:::warning
有限的硬件支持
:::

双向DShot通过[DSHOT_BIDIR_EN](../advanced_config/parameter_reference.md#DSHOT_BIDIR_EN)参数启用。系统使用[MOT_POLE_COUNT](../advanced_config/parameter_reference.md#MOT_POLE_COUNT)参数从接收到的eRPM数据计算实际电机转速。该参数必须正确设置以确保转速报告的准确性。