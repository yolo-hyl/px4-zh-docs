

# Holybro Microhard P900 Radio

Holybro Microhard P900 Radio 集成了 [Microhard Pico Serial](http://microhardcorp.com/P900.php) P900 RF模块，该模块能够在稳健且安全的网状、点对点或点对多点拓扑结构中提供高性能无线串行通信。  
它实现了机体与地面控制站（GCS）之间的MAVLink通信。

该无线电工作在902-928 MHz ISM频段，采用跳频扩频（FHSS）技术，可在使用串行接口的大多数设备类型之间提供可靠的无线异步数据传输。  
该无线电可在网状拓扑结构中配置为初级协调器、次级协调器、备用协调器或远程设备，或在PP或PMP拓扑结构中配置为主设备、中继器或远程设备。

这种灵活性对用户非常便利。  
该无线电可通过数据端口使用AT命令配置，或通过诊断端口使用 _PicoConfig_ 应用程序配置。

发射功率可通过软件在100mW至1W之间选择，通信距离可达40英里。  
单个地面站无线电可通过点对多点或网状拓扑结构与多架机体通信。  
机体必须配置不同的MAVLINK ID。

![Holybro Microhard Radio](../../assets/hardware/telemetry/holybro_microhard.jpg)

## 功能  

- USB Type-C端口，集成USB转UART转换器  
- 6位JST-GH连接器，可直接连接到各种符合Pixhawk标准的飞控器上的TELEM端口，如[Pixhawk 4](../flight_controller/pixhawk4.md) & [Pixhawk 5X](../flight_controller/pixhawk5x.md)  
- 内置高压BEC，支持DC7~35V电压输入  
- UART传输 & 三级RSSI LED指示器  
- 在无线电频谱的公共、免授权频段内传输  
- 透明、低延迟的链路速率，最高可达276 Kbps  
- 支持稳健的真正Mesh操作及自动路由  
- 32位CRC，可选重传和前向纠错  
- 独立的诊断端口，透明的远程诊断和在线网络控制

## 购买地点

- [Holybro Microhard P900 Telemetry Radio (100mW - 1W)](https://holybro.com/products/microhard-radio)

## 规格

<img src="../../assets/hardware/telemetry/holybro_microhard_spec.png" width="500px" title="Holybro Microhard P900 Diagnosis" />

## 连接

#### 机体无线电

该无线电模块配备一根6针JST GH电缆，可以连接到符合Pixhawk连接器标准的飞控上的`TELEM1`端口。
该无线电必须通过4针JST-GH XT30电源线单独供电(7-35VDC)。

#### 地面站无线电

该无线电设备内置USB转UART转换器，地面无线电可以通过USB C连接到地面站。  
该无线电必须通过XT30电源线（7-35VDC）单独供电。

## 设置/配置

Holybro Microhard P900 无线电在出厂时已配置为点对点操作模式和 57600 波特率的串口速率。  
这使得它们能够直接连接到 PX4 的 `TELEM1` 端口并 **无需任何额外配置** 即可使用 _QGroundControl_。

::: info
您可以使用不同的波特率、模式或飞控端口。  
唯一的要求是地面无线电、空中无线电、PX4 和 QGroundControl 必须全部设置为相同的波特率。
:::

[Microhard Serial Telemetry Radios > Configuration](../telemetry/microhard_serial.md#configuration) 说明了如何配置无线电、_QGroundControl_ 和 PX4。

要通过 _PicoConfig_ 应用程序（如上述链接所述）配置无线电，必须通过诊断端口连接：

<img src="../../assets/hardware/telemetry/holybro_microhard_uart converter.png" width="500px" title="Holybro Microhard P900 诊断" />

诊断端口使用 4 位 JST SH 连接器。  
如果您使用 _PicoConfig_ 应用程序或特殊诊断命令配置无线电，应连接到此端口。  
诊断端口兼容 3.3V 逻辑电平。  
连接无线电和计算机需要 USB 转串口板。  
您可以购买 [Holybro UART to USB Converter](https://holybro.com/products/uart-to-usb-converter)。

_Pico Config_ 会自动检测并连接到配置端口。  
调整设置以使波特率与 PX4（及地面站）匹配。

在给无线电上电时按住 **Config** 按钮，将会启动进入 COMMAND 模式：默认串口接口将被激活并临时设置为默认串口参数 9600/8/N/1。

注意，也可以通过数据端口使用 AT 命令配置无线电。

### 默认配置

出厂时的默认遥控器配置如下方的_PicoConfig_所示（在固件更新或重置遥控器后，可能需要重新配置）。

<img src="../../assets/hardware/telemetry/holybro_pico_config.png" width="400px" title="Holybro Pico Config" />
<img src="../../assets/hardware/telemetry/holybro_pico_config1.png" width="400px" title="Holybro Pico Config" />

在点对点操作模式下，系统必须有一个主设备提供网络同步，因此其中一个遥控器应配置为PP master，另一个应配置为PP remote。

## 状态LED

<img src="../../assets/hardware/telemetry/holybro_microhard_leds.png" width="600px" title="Holybro Pico Config" />

P900数传模块有6个状态LED：3个蓝色、2个橙色和1个绿色。
不同LED的含义如下：

- 电源LED（绿色）
  - 当P900数传模块连接电源（7-35VDC）时，该LED会亮起。
- TX LED（橙色）
  - 该LED亮起时，表示数传模块正在通过无线方式传输数据。
- RX LED（橙色）
  - 该LED表示数传模块已同步并接收到有效数据包。
- RSSI LED（3个蓝色）
  - 随着接收信号强度增加，从最左侧开始，激活的
- RSSI LED
  数量会增加。信号强度基于最后四个有效接收且CRC正确的数据包计算得出。RSSI值在S123中报告。

<img src="../../assets/hardware/telemetry/holybro_microhard_led_status.png" width="600px" title="Holybro Pico Config" />

<img src="../../assets/hardware/telemetry/holybro_microhard_rssi.png" width="600px" title="Holybro Pico Config" />

### 引脚分配

#### 诊断端口

| 引脚       | 信号 | 电压 |
| --------- | ------ | ------- |
| 1         | NC     | --      |
| 2 (黑色) | RX     | +3.3V   |
| 3 (黑色) | TX     | +3.3V   |
| 4 (黑色) | GND    | GND     |

#### 数据端口

| 引脚       | 信号 | 电压 |
| --------- | ------ | ------- |
| 1 (红色)   | NC     | --      |
| 2 (黑色) | RX     | +3.3V   |
| 3 (黑色) | TX     | +3.3V   |
| 4 (黑色) | CTS    | +3.3V   |
| 5 (黑色) | RTS    | +3.3V   |
| 6 (黑色) | GND    | GND     |

#### 电源端口

| 引脚       | 信号 | 电压 |
| --------- | ------ | ------- |
| 1(红色)    | BAT+   | 7-35V   |
| 2 (红色)   | BAT+   | 7-35V   |
| 3 (黑色) | BAT-   | GND     |
| 4 (黑色) | BAT-   | GND     |

<img src="../../assets/hardware/telemetry/holybro_microhard_pinout.jpg" width="600px" title="Holybro Pico 配置" />

### 尺寸

<img src="../../assets/hardware/telemetry/holybro_microhard_dimension.png" width="600px" title="Holybro Pico Config" />

### 功率消耗

- 供电电压：4针JST-GH至XT30（含）的直流7~35V
- 发射电流：200毫安/7伏特（20dBm）
  - 350毫安/7伏特（27dBm）
  - 800毫安/7伏特（30dBm）
- 接收电流：100毫安
- 重量：42克（不含天线）

## 进一步信息

- [Microhard Radio](https://docs.holybro.com/telemetry-radio/microhard-radio) (docs.holybro.com)
- [Holybro Microhard P900 下载](https://docs.holybro.com/telemetry-radio/microhard-radio/download) (手册和其他文档) (Holybro)