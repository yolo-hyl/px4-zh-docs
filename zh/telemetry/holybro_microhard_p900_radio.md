# Holybro Microhard P900 无线电

[Holybro Microhard P900 无线电](https://holybro.com/products/microhard-radio)集成了[Microhard Pico Serial](http://microhardcorp.com/P900.php) P900 RF模块，该模块能够在稳健且安全的网状、点对点或点对多点拓扑结构中提供高性能的无线串行通信。
它实现了机体与地面站（GCS）之间的MAVLink通信。

该无线电在902-928 MHz ISM频段内工作，采用跳频扩频（FHSS）技术，可在大多数使用串口的设备间提供可靠的异步数据传输。
该无线电可在网状拓扑结构中配置为初级协调器、次级协调器、备用协调器或远程节点，或在PP/PMP拓扑结构中配置为主站、中继或远程节点。

这种多功能性对用户非常方便。
该无线电可通过数据端口使用AT命令进行配置，或通过诊断端口使用_PicoConfig_应用程序进行配置。

发射功率可通过软件在100mW至1W之间选择，通信距离最远可达40英里。
单个地面站无线电可通过点对多点或网状结构与多个机体通信。
机体必须具有不同的MAVLINK ID。

![Holybro Microhard 无线电](../../assets/hardware/telemetry/holybro_microhard.jpg)

## 特性

- USB Type-C端口，集成USB转UART转换器
- 6位JST-GH连接器，可直接连接到各种Pixhawk标准飞控器的TELEM端口，如[Pixhawk 4](../flight_controller/pixhawk4.md)与[Pixhawk 5X](../flight_controller/pixhawk5x.md)
- 高压BEC，支持7~35V直流电压输入
- UART传输 & 三级RSSI LED指示灯
- 在无线电频谱的公共免许可频段内传输
- 透明、低延迟的数据传输速率最高可达276 Kbps
- 支持自动路由的稳健网状操作
- 32位CRC，可选重传和前向纠错
- 独立诊断端口，远程透明诊断和在线网络控制

## 购买渠道

- [Holybro Microhard P900 测距无线电 (100mW - 1W)](https://holybro.com/products/microhard-radio)

## 规格

<img src="../../assets/hardware/telemetry/holybro_microhard_spec.png" width="500px" title="Holybro Microhard P900 Diagnosis" />

## 连接方法

#### 机体无线电

该无线电附带6针JST GH电缆，可连接到符合Pixhawk接头标准的飞控器上的`TELEM1`端口。
无线电需通过4针JST-GH XT30电源线（7-35VDC）单独供电。

#### 地面站无线电

该无线电内置USB转UART转换器，可通过USB C连接到地面站。
无线电需通过XT30电源线（7-35VDC）单独供电。

## 设置/配置

Holybro Microhard P900无线电出厂时已配置为点对点操作模式和57600波特率。
这可使其直接连接到PX4的`TELEM1`端口和_QGroundControl_ **无需任何额外配置**。

::: info
您可以使用不同的波特率、模式或飞控端口。
唯一“要求”是地面无线电、机体无线电、PX4和QGroundControl必须设置为相同的波特率。
:::

[Microhard 串口测距无线电 > 配置](../telemetry/microhard_serial.md#configuration)说明了如何配置无线电、_QGroundControl_和PX4。

为了通过_PicoConfig_应用（如上文链接所述）配置无线电，必须通过诊断端口连接：

<img src="../../assets/hardware/telemetry/holybro_microhard_uart converter.png" width="500px" title="Holybro Microhard P900 Diagnosis" />

诊断端口使用4位JST SH连接器。
如果使用_PicoConfig_应用或特殊诊断命令配置无线电，应连接到此端口。
诊断端口兼容3.3V逻辑电平。
需要USB转串口板将无线电连接到计算机。
您可购买[Holybro UART转USB转换器](https://holybro.com/products/uart-to-usb-converter)。

_Pico Config_将自动识别并配置相关参数。

在按下电源按钮后，等待约30秒让系统初始化完成。
此时，您可以通过串口调试工具（如Putty或Screen）与无线电通信。

### 默认配置

- 波特率：57600
- 数据位：8
- 停止位：1
- 校验位：None

### 使用AT命令配置

1. 打开串口调试工具（如Putty或Screen）
2. 输入`AT`测试连接（应返回`OK`）
3. 输入`AT+NAME=YourName`设置设备名称
4. 输入`AT+ADDR=YourAddress`设置设备地址
5. 输入`AT+BAUD=9600`设置波特率
6. 输入`AT+SAVE`保存配置
7. 重启设备使配置生效

### 状态LED指示

| LED颜色 | 状态 | 含义 |
|---------|------|------|
| 绿色常亮 | 正常工作 | 无线电已通电并处于待机状态 |
| 绿色闪烁 | 通信中 | 无线电正在发送或接收数据 |
| 红色常亮 | 错误 | 配置错误或硬件故障 |

### 常见问题排查

1. **无法连接**：
   - 检查串口设置是否正确（波特率、数据位等）
   - 确保诊断端口连接正确
   - 重启无线电设备

2. **通信不稳定**：
   - 检查天线连接
   - 避开干扰源（如Wi-Fi路由器、微波炉）
   - 尝试降低波特率

3. **配置不生效**：
   - 确保输入了`AT+SAVE`保存配置
   - 重启设备使配置生效

### 进阶配置

对于需要高级设置的用户，可通过以下AT命令进行配置：

- `AT+POWER=20`：设置发射功率为20dBm
- `AT+RATE=276000`：设置数据传输速率为276 Kbps
- `AT+MODE=1`：切换到透明传输模式
- `AT+NETID=1234`：设置网络ID

### 电源管理

- **低功耗模式**：输入`AT+POWERSAVE=1`启用低功耗模式，可延长电池续航时间
- **自动关机**：输入`AT+AUTOOFF=60`设置60分钟自动关机

### 固件升级

1. 下载最新固件包：[Microhard Radio固件下载](https://docs.holybro.com/telemetry-radio/microhard-radio/download)
2. 使用`AT+UPDATE`命令启动升级模式
3. 通过串口上传固件文件
4. 等待升级完成并重启设备

### 技术支持

如需进一步帮助，请访问：
- [Microhard 无线电文档](https://docs.holybro.com/telemetry-radio/microhard-radio) (docs.holybro.com)
- [Holybro Microhard P900 下载](https://docs.holybro.com/telemetry-radio/microhard-radio/download)（手册及其他文档）（Holybro）