# J.Fi 无线遥测模块

J.MARPLE [J.Fi 遥测模块](https://jmarple.ai/j-fi/) 是一款紧凑轻量的无线通信设备，集成PCB天线或外置天线，可实现各类无人机飞控系统（FC）与地面站之间的无缝遥测连接。

该模块采用Pixhawk标准JST 6针 `TELEM` 接口，兼容所有基于PX4的飞控系统。支持即插即用的 `TELEM1` 接口默认设置，无需额外配置。

J.Fi 遥测模块在使用PCB集成天线时可提供约500米可靠通信距离。工作在2.4GHz频段，支持全球无限制使用。

![J.Fi Wireless Telemetry Module](../../assets/hardware/telemetry/jmarple/jfi_telemetry_module.png)

## 购买渠道

- [https://jmarple.ai/j-fi/](https://jmarple.ai/j-fi/)

## 技术规格

### 无线性能

- **频段:** 2.4GHz  
- **速率:** 最高11 Mbps（可调）  
- **距离:** 最远1000米（环境相关）  
- **负载能力:** 最高1024字节  

### 网络方案

- **支持拓扑:** 1:1, 1:N, N:N  
- **冲突管理:** 时隙响应延迟  

### 用户友好功能

- **按钮:** 配对与模式切换  
- **LED指示灯:** 实时状态更新  
- **配置:** 浏览器设置  
- **Micro USB接口:** 连接PC或地面站  

## 广播通信

启用默认设置后，设备会自动向所有附近J.Fi设备广播数据。将外部设备或系统连接到**广播端口**，无需额外设置。

![J.Fi Wireless Telemetry Broadcast Communication](../../assets/hardware/telemetry/jmarple/jfi_telemetry_broadcast.png)

## 配对通信

- 模块需先完成初始配对流程  
- 配对后，通信将**限制在已配对的J.Fi设备**之间。将外部设备或系统连接到**配对端口**  

![J.Fi Wireless Telemetry Paired Communication](../../assets/hardware/telemetry/jmarple/jfi_telemetry_paired.png)

### 1:1 配对

![J.Fi Wireless Telemetry Buttons](../../assets/hardware/telemetry/jmarple/jfi_telemetry_buttons.png)

- **在每个设备上:** 按住按钮A，然后点击RST按钮。  
  当LED1闪烁时释放按钮A  
  - 所有设备将进入配对模式  
- 选择一个模块，双击按钮A  
- 在其他模块上，点击按钮A一次  
- 在第一个模块上，再次点击按钮A完成配对  
  - 配对完成  

### 1:N 配对

- **在每个设备上:** 按住按钮A，然后点击RST按钮。  
  当LED1闪烁时释放按钮A  
  - 所有设备将进入配对模式  
- **主模块(1):** 双击按钮A  
- **从模块(N):** 在每个模块上点击按钮A一次以配对  
- **主模块(1):** 再次点击按钮A完成配对  
  - 配对完成  

<lite-youtube videoid="CnjhTfvARmw" title="J.Fi Wireless Telemetry Module Pairing Guide"/>

## PX4 设置

J.Fi 与连接到 `TELEM1` 端口的 PX4 兼容，无需额外连接即可即插即用。

若需使用其他端口，需为串口分配MAVLink实例（参见[MAVLink Peripherals (GCS/OSD/Companion)](../peripherals/mavlink_peripherals.md)）（可能需要取消当前占用该端口的设备）。

### 一对一 (1:1) 配置

`TELEM1` 端口默认使用 `57600` 波特率（J.Fi默认匹配）。此默认速率针对低成本低功耗遥测无线电校准。虽然适用于1:1配置，J.Fi 也可支持更高速率（如 `115200`）。

若需更改波特率：

1. 如果使用 `TELEM1` 端口，修改 [SER_TEL1_BAUD](../advanced_config/parameter_reference.md#SER_TEL1_BAUD)（其他端口参见[Serial Port Configuration](../peripherals/serial_configuration.md)）  
2. 在[J.Fi配置](#j-fi-configuration)中同步更新波特率  

### 一对多 (1:N) 配置

一对多(1:N)配置**强烈建议**使用更高波特率以确保数据稳定接收。所有J.Fi设备应设置为相同波特率（即使设备使用不同波特率通信可能仍可工作）。需在PX4和J.Fi模块中按照上文说明进行修改。

还需确保MAVLink网络中所有机体的**系统ID** ([MAV_SYS_ID](../advanced_config/parameter_reference.md#MAV_SYS_ID)) 唯一。

![J.Fi Wireless Telemetry Broadcast Communication](../../assets/hardware/telemetry/jmarple/jfi_telemetry_usage.png)

<lite-youtube videoid="tPeJA2gn7Zw" title="Simultaneous Control using J.Fi Wireless Telemetry Module"/>

## QGroundControl 配置

J.Fi 会即插即用连接到 **QGroundControl**，连接方式与SiK Radio相同。

若波特率从57600更改，需创建新连接配置：

1. 在QGC中禁用SiK Radio (**Application Settings → General → AutoConnect**)  
2. 创建新连接配置：  
   - 进入 **Application Settings → Comms Links**  
   - 点击 **Add**  
   - 设置 **Type** 为 **Serial**，配置 **Serial Port** 和 **Baud Rate**  
3. 保存并应用新配置  

## J.Fi 配置

- 按住按钮A，通过Wi-Fi连接到模块配置页面（SSID: J-FI-XXXXX）  
- 在浏览器中输入 `192.168.4.1`  
- 配置完成后按RST按钮重启  

## 进一步信息

- [开发文档](https://github.com/jmarple/jfi)  
- [用户手册](https://jmarple.ai/docs)