# MindRacer 硬件

:::warning
PX4 不生产此（或任何）自动驾驶仪。
如有硬件支持或合规性问题，请联系[制造商](http://mindpx.net)。
:::

AirMind<sup>&reg;</sup> [MindRacer](http://mindpx.net) 系列是用于微型无人机的全堆叠式飞行平台。  
目前该平台有两款开箱即用的机体：[MindRacer 210](../complete_vehicles_mc/mindracer210.md) 和 [NanoMind 110](../complete_vehicles_mc/nanomind110.md)。

![MindRacer](../../assets/hardware/hardware-mindracer.png)

::: info
此飞控是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 快速摘要

MindRacer 是基于 [MindPX](../flight_controller/mindpx.md) 的全堆叠式微型无人机飞行平台。  
_MindRacer_ 在缩小体积的同时进一步强调模块化设计。MindRacer 是一个平台而非飞控。

MindRacer 实现了 SEP（无焊接端口）和 WEP（无布线协议）概念。  
在 SEP 和 WEP 出现前，焊接和布线一直是无人机制造和调试中的主要难题和效率杀手。

::: info
主要硬件文档[在此](http://mindpx.net/assets/accessories/mindracer_spec_v1.2.pdf)。
:::

- 超小型尺寸，重量约6g  
- 高性能 STM32F427 168MHz 浮点处理器，极快的油门响应  
- 支持 OneShot 电调  
- 支持 PPM/SBUS/DSM 遥控接收机，支持 D.Port/S.Port/Wifi 通信  
- 内置飞行数据记录器  
- 支持 IMU 隔离  
- 符合 DroneCode<sup>&reg;</sup> 标准的连接器  

|                    项目                    |                      描述                      |
| :----------------------------------------: | :--------------------------------------------: |
|        飞控/处理器         |                       F427VIT6                       |
|                   重量                   |                          ~6g                         |
|                 尺寸                  |                        35x35mm                       |
|                PWM 输出口                 |                       最多 6 个                      |
|                    IMU                    |                         10DOF                        |
|               IMU 隔离               |                     是/可选                      |
|               遥控接收机               |             S.BUS/PPM/DSM/DSM2/DSMX/SUMD             |
|                 通信                  | FrSky<sup>&reg;</sup> D.Port, S.Port, Wifi, 3DR 无线电 |
| 内置 TF 卡飞行数据记录器 |                          是                          |
|             OneShot 电调支持             |                          是                          |
|              扩展槽              |                      2x7(针)x2                      |
|          内置实时时钟          |                          是                          |
|                 连接器                  |      JST GH（符合 DroneCode 标准）       |

## 快速开始

### 引脚分布图

![Mindracer 引脚图](../../assets/hardware/hardware-mindracer-pinout.png)

### 如何构建固件

:::tip
大多数用户无需构建此固件！  
当连接合适硬件时，_QGroundControl_ 会自动预构建并安装。
:::

要为本目标[构建 PX4](../dev_setup/building_px4.md)：

```
make airmind_mindpx-v2_default
```

### 伴飞电脑连接

MindRacer 配有集成的 Adapt IO 板。

![集成的 Adapt IO 板](../../assets/hardware/hardware-mindracer-conn.png)

MindRacer 内置 UART-USB 转换器。  
要连接伴飞电脑，请将 MindRacer 叠加在接口板上，并将伴飞电脑连接到接口板的 USB 端口。  
最大波特率与 PX4 系列一致，最高可达 921600。

### 用户指南

::: info
用户指南[在此](http://mindpx.net/assets/accessories/mindracer_user_guide_v1.2.pdf)
:::

## 购买渠道

MindRacer 可在 [AirMind 商店](http://drupal.xitronet.com/?q=catalog) 购买。  
您也可以在 Amazon<sup>&reg;</sup> 或 eBay<sup>&reg;</sup> 找到 MindRacer。

## 支持

请访问 http://www.mindpx.org 获取更多信息。  
或发送邮件至 [support@mindpx.net](mailto::support@mindpx.net) 进行咨询或获取帮助。