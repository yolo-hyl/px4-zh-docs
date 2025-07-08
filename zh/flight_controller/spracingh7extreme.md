# SPRacingH7EXTREME (PX4 版本)

:::warning
PX4 不生产此（或任何）自动飞行控制器。
如需硬件支持或合规性问题，请联系[制造商](https://shop.seriouslypro.com)。
:::

[SPRacingH7EXTREME](https://shop.seriouslypro.com/sp-racing-h7-extreme) 是一款功能齐全的飞控/电调分配板，配备双 ICM20602 陀螺仪、H7 400/480Mhz(+) CPU、高精度 BMP388 气压计、SD 卡插槽、电流传感器、8 个易于访问的电机输出口、OSD、麦克风、音频输出等。

它适用于小型至大型四旋翼、固定翼飞机、八旋翼和更复杂的机体。建议配合独立电调使用，因其内置了电调分配板（PDB）。4in1 电调的接线也非常简单。

还有一个 12 针堆叠连接器，提供 4 个额外的电机输出口、SPI 和 UART 接口。

![SPRacingH7EXTREME PCB 顶视图](../../assets/flight_controller/spracingh7extreme/spracingh7extreme-top.jpg)

![SPRacingH7EXTREME PCB 底视图](../../assets/flight_controller/spracingh7extreme/spracingh7extreme-bottom.jpg)

::: info
此飞控器是[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)的。
:::

## 核心特性

- 主系统芯片：[STM32H750VBT6 rev.y/v](https://www.st.com/en/microcontrollers-microprocessors/stm32h750vb.html)
  - CPU：400/480Mhz(+) ARM Cortex M7 带单精度浮点运算单元（FPU）。(+ Rev V 芯片支持 480Mhz)
  - RAM：1MB
  - 16MB 外部闪存 4 位 QuadSPI，以内存映射模式用于代码和配置。
- 板载传感器：
  - 双陀螺仪（各 1xSPI，带独立中断信号，支持 32kHz 和 fsync）
  - 高精度 BMP388 气压计（I2C + 中断）
  - 110A 电流传感器
- 通过外部 8 针 IO 端口连接 GPS
- 音频/视频
  - 屏幕显示（OSD，专用 SPI，字符型，MAX7456）
  - 麦克风传感器
  - CPU DAC 输出音频
  - 麦克风/DAC 输出的音频混音器
- 接口
  - SD 卡（4 位 SDIO，非 1 位 SPI）
  - 红外应答器（iLAP 兼容）
  - 蜂鸣器电路
  - RSSI（模拟/PWM）
  - 12 个电机输出（4 个电机接点、4 个中间位置、4 个堆叠连接器）
  - 1x SPI 引出到堆叠连接器
  - 6 个串口（5x TX & RX，1x TX-only 双向用于数传）
  - 启动按钮（侧按）
  - 绑定/用户按钮（侧按）
  - 接收机端口（支持所有常用协议，无需反相器）
  - CAM 端口的 OSD 控制和视频输入
  - SWD 调试端口
- 视频输出 + 音频输出在 VTX 端口
- 带 OTG 功能的 USB（ID 和 VBUS 连接到 CPU）
- 电源系统
  - 集成 PDB
  - 2-6S BEC
  - TVS 保护二极管
  - 专用于陀螺仪的 500ma VREG，带陀螺仪噪声滤波电容
  - 专用于 CPU、气压计、麦克风等的第二个 500ma VREG
- 其他特性
  - 状态指示灯
  - LED 灯带支持（带良好布局的连接焊盘）
  - 可从 SD 卡或外部闪存启动
  - 可通过 SD 卡烧录
  - 顶部焊接设计
  - PCB 切口用于电池线
  - 无指南针，需使用连接到 GPS IO 端口的外置 GPS 带磁力计/指南针传感器
  - 同时支持 Betaflight 4.x+ 和 Cleanflight 4.x+
  - 由 Dominic Clifton 设计（Cleanflight 的创建者）
- 尺寸
  - 36x36mm，30.5\*30.5 安装孔距，M4 孔
  - 提供 M4 至 M3 的软安装垫圈

## 购买地点

SPRacingH7EXTREME 可从[Seriously Pro 商店](https://shop.seriouslypro.com/sp-racing-h7-extreme)购买。

::: info
购买时请选择 PX4 版本！
:::

## 手册、引脚定义和接线图

带引脚定义的手册可从[这里](http://seriouslypro.com/files/SPRacingH7EXTREME-Manual-latest.pdf)下载。
其他接线图请查看[SPRacingH7EXTREME 网站](http://seriouslypro.com/spracingh7extreme)。

## 致谢

此设计由 [Dominic Clifton](https://github.com/hydra) 创建
初始 PX4 支持由 [Igor-Misic](https://github.com/Igor-Misic) 提供