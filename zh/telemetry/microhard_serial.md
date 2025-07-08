# Microhard串口遥测无线电

[Microhard Pico串口无线电](http://microhardcorp.com/P900.php)集成了[Microhard Pico串口](http://microhardcorp.com/P900.php) P900射频模块。

这是一款体积小、成本低的无线电设备，支持点对点、点对多点和网状模式等多种模式。
其输出功率可配置，并且可以配置为使用前向纠错（FEC）。
无线电还可以订购支持加密通道的型号，但需注意出口限制。

制造商通常将无线电默认配置为点对点模式，并匹配PX4和_QGroundControl_所需的波特率（57600）。
这允许在连接到Pixhawk飞行控制器的常规遥测端口（`TELEM1`或`TELEM2`）时实现即插即用的遥测功能，并在_QGroundControl_中自动检测连接。

多家制造商基于这些无线电提供解决方案：

- [ARK Electron Microhard串口遥测无线电](../telemetry/ark_microhard_serial.md)
- [Holybro Microhard P900遥测无线电](../telemetry/holybro_microhard_p900_radio.md)

## 范围权衡

无线电的范围取决于多个因素，包括：波特率、输出功率、模式、是否启用前向纠错、是否启用加密、使用的天线等。

这些参数的选择需要权衡：

- 增加波特率会降低无线电范围。
- 增加无线电功率可增加范围，但会减少飞行时间。
- 点对多点模式可以实现一个地面站与多个机体通信，但会增加信道带宽。
- 网状配置提供相似的便利性和成本效益。

规格中提到的最大范围约为60公里。
ARK Electron建议在输出功率设置为1W时，使用默认设置的范围约为8公里。

## 配置

出于便利性，无线电通常默认配置以便与PX4和_QGroundControl_开箱即用。

开发者可以修改配置。
唯一的要求是：地面无线电、空中介质、PX4和_QGroundControl_必须设置为使用**相同**的波特率（当然，每个MAVLink系统必须具有唯一的系统ID）。

### PX4配置

PX4配置为使用`TELEM1`进行遥测无线电通信，默认波特率为57600。
您可以通过遵循[MAVLink外围设备](../peripherals/mavlink_peripherals.md)中的说明，将PX4配置为使用其他空闲串口和不同波特率。

### QGroundControl配置

QGroundControl会自动检测波特率为57000的串口遥测连接。

对于其他波特率，需要添加一个串口通信链路并设置相应速率。
参见[应用程序设置 > 通信链路](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/settings_view.html)。

### 无线电配置

Microhard串口无线电通过_PicoConfig_应用程序进行配置（仅限Windows）。
此应用程序可从以下链接下载：[PicoConfig-1.7.zip](https://arkelectron.com/wp-content/uploads/2021/04/PicoConfig-1.7.zip)（ARK Electron）或[picoconfig-1-10](https://docs.holybro.com/telemetry-radio/microhard-radio/download)（Holybro）。

在点对点工作模式下，系统需要一个主设备提供网络同步，因此一个无线电应配置为PP主设备，另一个配置为PP远程设备。

下图显示了连接到PX4和_QGroundControl_的默认无线电配置设置。

<img src="../../assets/hardware/telemetry/holybro_pico_config.png" width="400px" title="Holybro Pico Config" />
<img src="../../assets/hardware/telemetry/holybro_pico_config1.png" width="400px" title="Holybro Pico Config" />

[Pico Series P900.Operating Manual.v1.8.7](https://github.com/PX4/PX4-user_guide/raw/main/assets/hardware/telemetry/Pico-Series-P900.Operating-Manual.v1.8.7.pdf) 提供了关于无线电配置的更多信息（包括网状和多点模式）。

### 网状和多点模式

网状和点对多点模式受支持，但所有机体必须具有唯一的MAVLink ID。

经验表明：

- 在最高链路速率且无FEC的情况下，一个网状系统中最多可有201架机体，每秒传输80字节数据。
- 使用“共存系统”可以同时运行多个网络且无相互干扰。
  例如，部署超过500架机体时，需要部署三个P900网状协调器，每个协调器在其各自的本地网络中最多服务201架机体。