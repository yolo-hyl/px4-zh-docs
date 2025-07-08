# 将遥控接收机连接到基于Linux的PX4自动驾驶仪

本主题展示如何在基于Linux的PX4自动驾驶仪上连接并使用[支持的遥控接收机](../getting_started/rc_transmitter_receiver.md)到任意串口。

对于除S.Bus外的其他遥控类型，可以直接将接收机连接到串口，或通过USB到TTY串口线连接到USB（例如PL2302 USB转串口TTL转换器）。

::: info
对于S.Bus接收机（或编码器 - 例如Futaba、RadioLink等），通常需要通过[信号反转电路](#signal_inverter_circuit)连接接收机和设备，但其他设置相同。
:::

然后在设备上[启动PX4遥控驱动](#start_driver)，如下所示。

## 启动驱动 {#start_driver}

在特定UART（例如本例中的`/dev/ttyS2`）上启动遥控驱动：

```sh
rc_input start -d /dev/ttyS2
```

其他驱动用法请参见：[rc_input](../modules/modules_driver_radio_control.md#rc-input)。

## 信号反转电路（仅S.Bus） {#signal_inverter_circuit}

S.Bus是反向UART通信信号。

虽然某些串口/飞控可以读取反向UART信号，但大多数情况下需要在接收机和串口之间使用信号反转电路来解除信号反转。

:::tip
此电路也是通过串口或USB-to-TTY串口转换器读取S.Bus遥控信号所必需的。
:::

本节展示如何创建合适的电路。

### 所需组件

- 1x NPN晶体管（例如NPN S9014 TO92）
- 1x 10K电阻
- 1x 1K电阻

::: info
任何类型的晶体管均可使用，因为电流消耗非常小。
:::

### 电路图/连接方式

按照以下描述（并参考电路图）连接组件：

- S.Bus信号 &rarr; 1K电阻 &rarr; NPN晶体管基极
- NPN晶体管发射极 &rarr; GND
- 3.3VCC &rarr; 10K电阻 &rarr; NPN晶体管集电极 &rarr; USB-to-TTY rxd
- 5.0VCC &rarr; S.Bus VCC
- GND &rarr; S.Bus GND

![信号反转电路图](../../assets/sbus/driver_sbus_signal_inverter_circuit_diagram.png)

下图显示了面包板上的连接方式。

![信号反转面包板](../../assets/sbus/driver_sbus_signal_inverter_breadboard.png)