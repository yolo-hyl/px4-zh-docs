# TFSIK01 通信调制解调器

[TFSIK01](https://docs.thunderfly.cz/avionics/TFSIK01/) 是由 [ThunderFly](https://www.thunderfly.cz/) 开发的高性能开源遥测调制解调器。  
该设备旨在为无人机和地面站提供可靠的无线通信。  
其具备双天线分集、强抗干扰能力，且兼容 MAVLink 帧格式，是高要求无人机和机器人应用的理想选择。

![TFSIK01 与 USB-C 转换器搭配使用](../../assets/hardware/telemetry/thunderly_tfsik01_pair.jpg)

该调制解调器通过 JST-GH UART 接口即插即用，预配置为 433、868 和 915 MHz 频段（其他非标准频率可定制）。

## 购买渠道

- [Tindie 上的 TFSIK01A](https://www.tindie.com/products/34682/)  
- 可直接从 [ThunderFly](https://www.thunderfly.cz/contact-us.html) 购买 ([sale@thunderfly.cz](mailto:sale@thunderfly.cz))

## 功能特性

- 开源 SiK 固件  
- 双天线分集（自动切换）  
- 抗干扰及抗带外信号干扰  
- 跳频扩频（FHSS）  
- 支持自适应 TDM、LBT 和 AFA  
- 支持 MAVLink 协议  
- 最大 250 kbps 空中数据速率  
- 数公里传输距离  
- 与 Pixhawk 兼容的飞控即插即用  

## 技术参数

- 频率：433 MHz（默认）、868 MHz、915 MHz 或定制频率  
- 功率：最高 500 mW（27 dBm），可调节（默认 100mW）  
- 接口：JST-GH 6针 UART（3.3V）  
- 接口类型：双 MCX — 快速连接器，可减少碰撞损坏风险  
- 重量：18 g  

## LED 指示灯状态

- **绿色闪烁** – 正在建立连接  
- **绿色常亮** – 连接已建立  
- **红色闪烁** – 数据传输中  
- **红色常亮** – 固件更新模式  
- **橙色** – 表示选定的 RX/TX 天线  

## 连接飞控器

使用附带的 JST-GH 电缆连接到飞控器的 `TELEM1` 接口。  
如需使用其他 UART 接口，可能需要重新配置。

## 连接到 PC 或地面站

使用 [TFUSBSERIAL01](https://docs.thunderfly.cz/avionics/TFUSBSERIAL01/) USB-C 转 UART 适配器将调制解调器连接到 PC、平板或 Raspberry Pi。

## 包装内容

- 2× TFSIK01 调制解调器（含外壳）  
- 2× JST-GH 串口线  
- 1× TFUSBSERIAL01 USB-C 转 UART 转换器  
- 2× MCX 天线套件（可选）  

## 更多信息

如需详细规格、配置选项、固件更新和高级用法，请访问完整的 [TFSIK01 文档](https://docs.thunderfly.cz/avionics/TFSIK01/)