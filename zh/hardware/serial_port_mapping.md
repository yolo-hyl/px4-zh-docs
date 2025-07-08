# 串口映射

本主题展示如何确定USART/UART串口设备名称（如"ttyS0"）与飞行控制器上关联端口（如 `TELEM1`、`TELEM2`、`GPS1`、`RC SBUS`、`Debug console`）之间的映射关系。

这些说明用于生成飞行控制器文档中的串口映射表。  
例如: [Pixhawk 4 > 串口映射](../flight_controller/pixhawk4.md#serial-port-mapping)

::: info
每个端口分配的功能不一定要与名称匹配（多数情况下），其配置通过[串口配置](../peripherals/serial_configuration.md)设置。  
通常端口功能会配置为与名称一致，这就是为什么标记为 `GPS1` 的端口可以直接配合GPS使用的原因。
:::

## NuttX on STMxxyyy

<!-- 说明来自 DavidS 的链接: https://github.com/PX4/PX4-user_guide/pull/672#issuecomment-598198434 -->

本节展示如何通过检查板级配置文件获取STMxxyyy架构下NuttX构建的串口映射。  
说明以FMUv5为例，但同样适用于其他FMU版本/NuttX板。

### default.px4board

**default.px4board** 文件列出了多个串口映射（搜索文本 "SERIAL_PORTS"）。

来自 [/boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board):

```
CONFIG_BOARD_SERIAL_GPS1="/dev/ttyS0"
CONFIG_BOARD_SERIAL_TEL1="/dev/ttyS1"
CONFIG_BOARD_SERIAL_TEL2="/dev/ttyS2"
CONFIG_BOARD_SERIAL_TEL4="/dev/ttyS3"
```

或者通过 `make px4_fmu-v5 boardconfig` 启动 boardconfig 工具并访问串口菜单：

```
    Serial ports  --->
        (/dev/ttyS0) GPS1 tty port
        ()  GPS2 tty port
        ()  GPS3 tty port
        ()  GPS4 tty port
        ()  GPS5 tty port
        (/dev/ttyS1) TEL1 tty port
        (/dev/ttyS2) TEL2 tty port
        ()  TEL3 tty port
        (/dev/ttyS3) TEL4 tty port
        ()  TEL5 tty port
```

### nsh/defconfig

_nsh/defconfig_ 可让您确定定义的端口、它们是UART还是USART，以及USART/UART与设备的映射关系。  
您还可以确定哪个端口用于[串口/调试控制台](../debug/system_console.md)。

打开板级 defconfig 文件，例如：[/boards/px4/fmu-v5/nuttx-config/nsh/defconfig](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/nuttx-config/nsh/defconfig#L215-L221)

搜索文本 "ART" 直到找到类似 `CONFIG_STM32xx_USARTn=y` 的部分（其中 `xx` 是处理器类型，`n` 是端口号）。  
例如：

```
CONFIG_STM32F7_UART4=y
CONFIG_STM32F7_UART7=y
CONFIG_STM32F7_UART8=y
CONFIG_STM32F7_USART1=y
CONFIG_STM32F7_USART2=y
CONFIG_STM32F7_USART3=y
CONFIG_STM32F7_USART6=y
```

这些条目告诉您哪些端口被定义，以及它们是UART还是USART。

复制上面的段落并按 "n" 数字重新排序。  
同时将设备号 _ttyS**n**_ 按零基递增，以获得设备到串口的映射。

```
ttyS0 CONFIG_STM32F7_USART1=y
ttyS1 CONFIG_STM32F7_USART2=y
ttyS2 CONFIG_STM32F7_USART3=y
ttyS3 CONFIG_STM32F7_UART4=y
ttyS4 CONFIG_STM32F7_USART6=y
ttyS5 CONFIG_STM32F7_UART7=y
ttyS6 CONFIG_STM32F7_UART8=y
```

要获取DEBUG控制台的映射，我们搜索 [defconfig 文件](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/nuttx-config/nsh/defconfig#L212) 中的 `SERIAL_CONSOLE`。  
下面我们可以看到控制台在 UART7 上：

```
CONFIG_UART7_SERIAL_CONSOLE=y
```

### board_config.h

对于带有IO板的飞行控制器，通过 **board_config.h** 中的 `PX4IO_SERIAL_DEVICE` 确定PX4IO连接。

例如，[/boards/px4/fmu-v5/src/board_config.h](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/src/board_config.h#L59):

```
#define PX4IO_SERIAL_DEVICE            "/dev/ttyS6"
#define PX4IO_SERIAL_TX_GPIO           GPIO_UART8_TX
#define PX4IO_SERIAL_RX_GPIO           GPIO_UART8_RX
#define PX4IO_SERIAL_BASE              STM32_UART8_BASE
```

因此，PX4IO位于 `ttyS6`（我们还可以看到这映射到UART8，这已在前一节中确认）。

### 综合所有内容

最终的映射是：

```
ttyS0 CONFIG_STM32F7_USART1=y GPS1
ttyS1 CONFIG_STM32F7_USART2=y TEL1
ttyS2 CONFIG_STM32F7_USART3=y TEL2
ttyS3 CONFIG_STM32F7_UART4=y TEL4
ttyS4 CONFIG_STM32F7_USART6=y
ttyS5 CONFIG_STM32F7_UART7=y DEBUG
ttyS6 CONFIG_STM32F7_UART8=y PX4IO
```

在[飞行控制器文档](../flight_controller/pixhawk4.md#serial-port-mapping)中，结果表格如下：

| UART   | Device     | Port                  |
| ------ | ---------- | --------------------- |
| UART1  | /dev/ttyS0 | GPS                   |
| USART2 | /dev/ttyS1 | TELEM1 (流控制)       |
| USART3 | /dev/ttyS2 | TELEM2 (流控制)       |
| UART4  | /dev/ttyS3 | TELEM4                |
| USART6 | /dev/ttyS4 | RC SBUS               |
| UART7  | /dev/ttyS5 | 调试控制台            |
| UART8  | /dev/ttyS6 | PX4IO                 |

## 其他架构

::: info
欢迎贡献！
:::

## 参见

- [串口配置](../peripherals/serial_configuration.md)
- [MAVLink遥测（OSD/GCS）](../peripherals/mavlink_peripherals.md)