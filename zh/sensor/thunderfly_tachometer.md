# ThunderFly TFRPM01 转速计  

[TFRPM01](https://github.com/ThunderFly-aerospace/TFRPM01) 是一款小型、系统资源消耗低的转速计。  

该电路板本身不包含实际传感器，但可与多种不同类型的传感器/探头配合使用以进行转速计数。  
它配备了一个 I²C 接口用于连接到 PX4，并通过一个 3 针接口连接到实际传感器。  
此外，它还有一个 LED，提供基本的诊断信息。  

![TFRPM01A](../../assets/hardware/sensors/tfrpm/tfrpm01_electronics.jpg)  

::: info  
TFRPM01 传感器是开源硬件，可从 [ThunderFly s.r.o.](https://www.thunderfly.cz/) 商业购买（制造数据在 [GitHub](https://github.com/ThunderFly-aerospace/TFRPM01) 上可用）。  
:::  

## 硬件设置  

该电路板配备（两个直通）I²C 接口用于连接到 PX4，并有一个 3 针接口可用于连接各种传感器：  

- TFRPM01 可连接到任意 I²C 端口。  
- TFRPM01 有一个 3 针插头接口（带上拉输入），可用于连接不同类型的探头。  
  - 传感器/探头硬件需要脉冲信号。  
    信号输入接受 +5V TTL 电平或 [集电极开路](https://en.wikipedia.org/wiki/Open_collector) 输出。  
    最大脉冲频率为 20 kHz，占空比为 50%。  
  - 探头接口从 I²C 总线提供 +5V 电源，最大可用功率受 RC 滤波器限制（详见原理图）。  

TFRPM01A 电子设备配备了一个信号 LED，可用于检查探头是否正确连接。  
当脉冲输入接地或处于逻辑 0 时，LED 会亮起，因此可以通过手动旋转旋翼来检查探头是否正常工作。  

### 霍尔效应传感器探头  

霍尔效应传感器（磁敏型）适用于恶劣环境，例如灰尘、水等可能接触被测旋翼的场景。  

市面上有许多不同型号的霍尔效应传感器。  
例如，[55100 小型法兰安装接近传感器](https://m.littelfuse.com/media?resourcetype=datasheets&itemid=6d69d457-770e-46ba-9998-012c5e0aedd7&filename=littelfuse-hall-effect-sensors-55100-datasheet) 是一个不错的选择。  

![霍尔效应探头示例](../../assets/hardware/sensors/tfrpm/hall_probe.jpg)  

### 光学传感器探头  

也可使用光学传感器（根据测量需求不同，可能更适合）。  
透射式和反射式传感器均可用于生成脉冲信号。  

![透射式光学探头示例](../../assets/hardware/sensors/tfrpm/transmissive_probe.jpg)  

## 软件设置  

### 启动驱动程序  

驱动程序不会在任何飞行器框架中自动启动。  
您需要手动启动它，要么通过 [QGroundControl MAVLink Console](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html)，要么通过在 SD 卡上将驱动程序添加到 [启动脚本](../concept/system_startup.md#customizing-the-system-startup) 中。  

#### 从控制台启动驱动程序  

使用以下命令从 [控制台](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 启动驱动程序：  

```sh  
pcf8583 start -X -b <bus number>  
```  

其中：  

- `-X` 表示使用外部总线。  
- `<bus number>` 是设备连接的总线编号。  

::: info  
代码中的总线编号 `-b <bus number>` 可能与飞控上的总线标签不匹配。  
例如，使用 CUAV V5+ 或 CUAV Nano 时：  

| 飞控标签 | -b 编号 |  
| -------- | ------- |  
| I2C1     | -X -b 4 |  
| I2C2     | -X -b 2 |  
| I2C3     | -X -b 1 |  

`pcf8583 start` 命令会输出每个总线编号对应的飞控总线名称/标签。  
:::  

### 测试  

您可以通过多种方法验证计数器是否正常工作。  

#### PX4 (NuttX) MAVLink Console  

[QGroundControl MAVLink Console](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_console.html) 也可用于检查驱动程序是否运行以及它输出的 UORB 话题。  

要检查 TFRPM01 驱动程序的状态，请运行命令：  

```sh  
pcf8583 status  
```  

如果驱动程序正在运行，将打印 I²C 端口及其他运行实例的基本参数。  
如果驱动程序未运行，可以按照上述步骤启动它。  

[listener](../modules/modules_command.md#listener) 命令可用于监控运行驱动程序的 RPM UORB 消息。  

```sh  
listener rpm  
```  

要周期性显示，可以在命令后添加 `-n 50` 参数，这将打印接下来的 50 条消息。  

#### QGroundControl MAVLink Inspector  

QGroundControl [Mavlink Inspector](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html) 可用于检查 PX4 输出的 MAVLink 消息。  
通过该工具可以验证转速计数据是否正确传输。  

### 校准与配置  

在使用转速计前，需要进行校准以确保测量精度。校准步骤通常包括：  
1. 将探头安装在目标位置并确保与旋翼对齐。  
2. 在 PX4 中启用转速计模块并设置相关参数（如脉冲计数模式）。  
3. 通过手动旋转旋翼或使用测试设备验证输出值与预期转速是否匹配。  

### 故障排除  

如果转速计无法正常工作，请检查以下内容：  
- 探头与旋翼的间隙是否在规格范围内（通常为 1-3mm）。  
- I²C 总线连接是否稳固，且未与其他设备发生冲突。  
- 驱动程序参数（如 `-b` 指定的总线编号）是否与实际硬件配置匹配。  
- 使用 `log` 工具检查 PX4 日志中是否有与转速计相关的错误信息。  

### 高级功能  

TFRPM01 支持以下高级功能：  
- **多旋翼支持**：通过配置多个探头，可同时监测多个旋翼的转速。  
- **数据记录**：将转速数据记录到 SD 卡中，用于后续分析。  
- **动态校准**：在飞行过程中自动调整校准参数以适应环境变化。  

### 兼容性  

该转速计已验证与以下 PX4 固件版本兼容：  
- PX4 v1.12 及以上版本  
- NuttX 内核  
- 支持 I²C 接口的飞控硬件（如 Pixhawk 4、Cube Orange 等）  

如需进一步了解技术细节或获取最新版本，请访问 [TFRPM01 GitHub 页面](https://github.com/ThunderFly-aerospace/TFRPM01)。