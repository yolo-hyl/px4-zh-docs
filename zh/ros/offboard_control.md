# 离板控制

::: warning
[离板控制](../flight_modes/offboard.md)具有危险性。
开发者在进行离板飞行前必须确保已做好充分的准备、测试和安全措施。
:::

离板控制的核心理念是通过运行在自动飞行器外部的软件控制PX4飞行栈。
这通过MAVLink协议实现，具体使用以下消息：
[SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED) 和 [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET)。

## 离板控制固件设置

在开始离板开发前，需要在固件侧完成两项设置。

### 启用遥控器覆盖

在 _QGroundControl_ 中设置 [COM_RC_OVERRIDE](../advanced_config/parameter_reference.md#COM_RC_OVERRIDE) 参数，当遥控器摇杆移动时可自动从离板模式（或任何模式）切换到位置模式。
这是确保操作员能轻松接管机体并切换到最安全飞行模式的最佳方式。

### 映射遥控器开关到离板模式激活

在 _QGroundControl_ 中设置 [RC_MAP_OFFB_SW](../advanced_config/parameter_reference.md#RC_MAP_OFFB_SW) 参数，将遥控器通道绑定到离板模式激活。
这可用于在离板模式和由模式开关([RC_MAP_MODE_SW](../advanced_config/parameter_reference.md#RC_MAP_MODE_SW))定义的模式之间切换。
也可以通过地面站/MAVLink切换到离板模式，因此不是"必需"操作。

需要注意这种机制比使用[遥控器覆盖](#enable-rc-override)切换出离板模式的"安全性"要低，因为切换后的模式是不可预测的。

### 启用伴飞计算机接口

在连接伴飞计算机的串口上启用MAVLink（参见[伴飞计算机](../companion_computer/index.md)）。

## 硬件设置

离板通信通常有三种实现方式：

### 串口无线电

1. 一个连接到自动飞行器UART端口的设备
2. 一个连接到地面站计算机的设备

示例无线电包括：
- [Lairdtech RM024](http://www.lairdtech.com/products/rm024)
- [Digi International XBee Pro](http://www.digi.com/products/xbee-rf-solutions/modules)

[![Mermaid graph: mavlink channel](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVEQ7XG4gIGduZFtHcm91bmQgU3RhdGlvbl0gLS1NQVZMaW5rLS0-IHJhZDFbR3JvdW5kIFJhZGlvXTtcbiAgcmFkMSAtLVJhZGlvUHJvdG9jb2wtLT4gcmFkMltWZWhpY2xlIFJhZGlvXTtcbiAgcmFkMiAtLU1BVkxpbmstLT4gYVtBdXRvcGlsb3RdOyIsIm1lcm1haWQiOnsidGhlbWUiOiJkZWZhdWx0In0sInVwZGF0ZUVkaXRvciI6ZmFsc2V9)

<!-- original mermaid graph
graph TD
  gnd[Ground Station] --MAVLink--> rad1[Ground Radio]
  rad1 --Radio Protocol--> rad2[Vehicle Radio]
  rad2 --MAVLink--> autp[Autopilot]
-->

### 伴飞计算机系统

[![Mermaid graph: companion computer](https://mermaid.ink/img/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHFnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHFnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVERcbiAgc3ViZ3JhcGggR3JvdW5kICBTdGF0aW9uXG4gIGduZFtST1MgRW5hYmxlZCBDb21wdXRlcl0gLS0tIHFnY1txR3JvdW5kQ29udHJvbF1cbiAgZW5kXG4gIGduZCAtLU1BVkxpbmsvVURQLS0-IHdbV2lGaV07XG4gIHFnYyAtLU1BVkxpbmstLT4gdztcbiAgc3ViZ3JhcGggVmVoaWNsZVxuICBjb21wW0NvbXBhbmlvbiBDb21wdXRlcl0gLS1NQVZMaW5rLS0-IHVhcnRbVUFSVCBBZGFwdGVyXVxuICB1YXJ0IC0tLSBBdXRvcGlsb3RcbiAgZW5kXG4gIHcgLS0tIGNvbXAiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9LCJ1cGRhdGVFZGl0b3IiOmZhbHNlfQ)

<!-- original mermaid graph
graph TD
  subgraph Ground  Station
  gnd[ROS Enabled Computer] --- qgc[qGroundControl]
  end
  gnd --MAVLink/UDP-- > w[WiFi];
  qgc --MAVLink-- > w;
  subgraph Vehicle
  comp[Companion Computer] --MAVLink-- > uart[UART Adapter]
  uart --- Autopilot
  end
  w --- comp
-->