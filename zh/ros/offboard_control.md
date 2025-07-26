

# 机外控制

::: warning
[机外控制](../flight_modes/offboard.md)具有危险性。
开发者在进行机外飞行前必须确保已做好充分的准备、测试和安全措施。
:::

机外控制的核心思想是通过运行在自驾仪外部的软件来控制PX4飞行栈。  
这是通过MAVLink协议实现的，具体使用以下消息：  
[SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED) 和 [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET)。

## 外部控制固件设置

在开始外部控制开发之前，您需要在固件端设置两件事。

### 启用RC接管

在 _QGroundControl_ 中，你可以设置 [COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) 参数，当移动遥控器摇杆时，可自动从外部控制模式（或任何模式）切换到定位模式。  
这是确保操作员能够轻松接管机体并切换到最安全飞行模式的最佳方式。

### 将RC开关映射到外部模式激活

在 _QGroundControl_ 中，您可以将 [RC_MAP_OFFB_SW](../advanced_config/parameter_reference.md#RC_MAP_OFFB_SW) 参数设置为用于激活外部模式的RC通道。  
这可以用于在外部模式和模式开关 ([RC_MAP_MODE_SW](../advanced_config/parameter_reference.md#RC_MAP_MODE_SW)) 设置的模式之间切换。  
您也可以通过GCS/MAVLink切换到外部模式，因此这并非"必须"。

还需注意，与使用 [RC Override](#启用RC接管) 切出外部模式相比，此机制的安全性较低，因为切换到的模式不可预测。

### 启用伴飞计算机接口

在连接到伴飞计算机的串口上启用MAVLink（参见[Companion Computers](../companion_computer/index.md)）。

## 硬件设置

通常有三种设置外部通信的方法。

### 串口无线电

1. 一个连接到自动驾驶仪的UART端口  
2. 一个连接到地面站计算机  

   示例无线电设备包括：  

   - [Lairdtech RM024](http://www.lairdtech.com/products/rm024)  
   - [Digi International XBee Pro](http://www.digi.com/products/xbee-rf-solutions/modules)  

[![Mermaid图表：mavlink通道](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)  

<!-- original mermaid graph  
graph TD;  
  gnd[Ground Station] --MAVLink-- > rad1[Ground Radio];  
  rad1 --RadioProtocol-- > rad2[Vehicle Radio];  
  rad2 --MAVLink-- > a[Autopilot];  
-->

### 机载处理器

安装在机体上的小型计算机，通过串口或以太网口与飞控连接。  
具体选择取决于您希望在向飞控发送指令之外，进行哪些额外的机载处理。  
一些示例可参考 [Companion Computers](../companion_computer/index.md#companion-computer-options)。

[![Mermaid diagram: Companion mavlink](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGNvbXBbQ29tcGFuaW9uIENvbXB1dGVyXSAtLU1BVkxpbmstLT4gdWFydFtVQVJUIEFkYXB0ZXJdO1xuICB1YXJ0IC0tTUFWTGluay0tPiBBdXRvcGlsb3Q7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGNvbXBbQ29tcGFuaW9uIENvbXB1dGVyXSAtLU1BVkxpbmstLT4gdWFydFtVQVJUIEFkYXB0ZXJdO1xuICB1YXJ0IC0tTUFWTGluay0tPiBBdXRvcGlsb3Q7IiwibWVybWFpZCI6eyJ0aGVtZSI6ImRlZmF1bHQifSwidXBkYXRlRWRpdG9yIjpmYWxzZX0)

<!-- original mermaid graph
graph TD;
  comp[Companion Computer] --MAVLink-- > uart[UART Adapter];
  uart --MAVLink-- > Autopilot;
-->

### 机载处理器和WiFi链接到ROS（**_推荐_**）

安装在机体上的小型计算机通过UART转USB适配器连接到飞控，同时通过WiFi连接到运行ROS的地面站。
这可以是上述部分中的任何计算机配合WiFi适配器使用。

[![Mermaid图：ROS](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHxnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHxnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHxnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHxnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

<!-- 原始mermaid图
graph TD
  subgraph 地面站
  gnd[ROS启用计算机] --- qgc[qGroundControl]
  end
  gnd --MAVLink/UDP-- > w[WiFi];
  qgc --MAVLink-- > w;
  subgraph 机体
  comp[伴飞计算机] --MAVLink-- > uart[UART适配器]
  uart --- 自动驾驶仪
  end
  w --- comp
-->