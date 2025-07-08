# ARK Electron Microhard 串口遥测无线电

_ARK Electron Microhard 串口遥测无线电_ 集成了 [Microhard Pico 串口](http://microhardcorp.com/P900.php) P900 RF 模块。  
该模块可用于实现机体与地面站之间的 MAVLink 通信。

Microhard Pico 串口无线电最大输出功率可达 1 瓦，支持点对点、点对多点和网状网络模式。  
Microhard Pico 无线电还可选配 AES-256 加密功能。

当输出功率设置为 1W 且使用默认设置时，最大传输距离约 8 公里（5 英里）。  
通过点对多点或网状网络模式，一个地面站无线电可同时与多个机体通信。  
机体必须设置不同的 MAVLINK ID。

![Microhard 无线电](../../assets/hardware/telemetry/ark_microhard_serial.jpg)

## 购买渠道

- [1W 900MHz 串口遥测空用无线电](https://arkelectron.com/product/1w-900mhz-serial-telemetry-air-radio/) (机体)
- [1W 900MHz USB 串口遥测地用无线电](https://arkelectron.com/product/1w-900mhz-serial-telemetry-ground-radio/) (地面站)
- [1W 2.4GHz 串口遥测空用无线电](https://arkelectron.com/product/1w-2400mhz-serial-telemetry-radio/) (机体)
- [1W 2.4GHz USB 串口遥测地用无线电](https://arkelectron.com/product/1w-2400mhz-usb-serial-telemetry-radio/) (地面站)

## 连接方式

### 机体无线电

将机体无线电连接到飞控的 `TELEM1` 接口。  
为此目的提供了标准Pixhawk 6针JST GH遥测线缆。

如果输出功率设置低于 100mW，可通过遥测线缆为无线电供电。  
对于更高功率输出，必须通过 2针 Molex Nano-Fit 接口单独供电（例如从电池供电）。

![机体上的Microhard 无线电](../../assets/hardware/telemetry/microhard_serial_on_vehicle.jpg)

### 地面站无线电

通过 USB C 接口将地面无线电连接到地面站。  
当使用 USB PD 供电时，无需单独为无线电供电（1W功率可通过USB供电）。

## 设置/配置

无线电默认配置为点对点模式和 57600 波特率。  
这使其可直接连接到 PX4 的 `TELEM1` 接口并 _QGroundControl_ **无需额外配置**。

::: info
您可以使用不同的波特率、模式或飞控端口。  
唯一"要求"是地面无线电、空用无线电、PX4 和 QGroundControl 必须设置相同的波特率。
:::

[Microhard 串口遥测无线电 > 配置](../telemetry/microhard_serial.md#configuration) 说明了如何配置无线电、_QGroundControl_ 和 PX4。

ARK Electron 无线电必须按照以下方式连接到运行 _PicoConfig_ 配置工具的计算机：

- 配置机体无线电时，需在无线电的 3针 JST-GH 配置口与运行 _Pico Config_ 的 Windows PC 之间连接 FTDI 适配器（无线电必须通电，可通过电池或飞控的 `TELEM1` 接口供电）。

  ![Ark Microhard 串口 - 接口](../../assets/hardware/telemetry/ark_microhard_serial_ports.jpg)

  _Pico Config_ 将自动检测无线电。  
  调整波特率设置以匹配 PX4（和地面站无线电）。

- 地面站无线电的 USB C 接口可用于配置无线电（以及遥测数据传输）。  
  _Pico Config_ 将自动检测并连接到配置端口。  
  调整设置使波特率与 PX4 匹配。

当无线电和 PX4 都配置为使用相同波特率后，即可通过无线电将 QGroundControl 连接到机体。

### 默认配置

出厂时的默认无线电配置如下所示于 _PicoConfig_ 中。

![Pico 配置](../../assets/hardware/telemetry/pico_configurator.png)

## 更多信息

- [Pico Config 1.7](https://arkelectron.com/wp-content/uploads/2021/04/PicoConfig-1.7.zip) - 无线电配置工具