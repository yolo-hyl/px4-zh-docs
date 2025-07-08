# Holybro Kakute H743-Wing 

<Badge type="tip" text="PX4 v1.16" />

:::warning
PX4 does not manufacture this (or any) autopilot.
Contact the [manufacturer](https://holybro.com/) for hardware support or compliance issues.
:::

[Holybro Kakute H743 Wing](https://holybro.com/products/kakute-h743-wing) 是一款专为固定翼和垂直起降（VTOL）应用设计的全功能飞控。它搭载运行在480 MHz的STM32 H743处理器，支持CAN总线，配备双摄像头支持及切换功能、ON/OFF Pit Switch、5V、6V/8V、9V/12 BEC，以及即插即用的GPS、CAN和I2C接口。

::: info
此飞控获得[制造商支持](../flight_controller/autopilot_manufacturer_supported.md)。
:::

## 购买渠道

该飞控板可从以下商店购买（例如）：

- [Holybro](https://holybro.com/products/kakute-h743-wing)

## 连接器与引脚

| 引脚              | 功能                          | PX4 默认配置                |
| ---------------- | --------------------------------- | -------------------------- |
| GPS 1            | USART1 和 I2C1                   | GPS1                       |
| R2, T2           | USART2 RX 和 TX                  | GPS2                       |
| R3, T3           | USART3 RX 和 TX                  | TELEM1                     |
| R5, T5           | USART5 RX 和 TX                  | TELEM2                     |
| R6, T6           | USART6 RX 和 TX                  | RC (PPM, SBUS 等) 输入     |
| R7, T7, RTS, CTS | UART7 RX 和 TX（带流控制）        | TELEM3                     |
| R8, T8           | UART8 RX 和 TX                   | Console                    |
| Buz-, Buz+       | 压电蜂鸣器                       |                            |
| M1 至 M14        | 电机信号输出                     |                            |

## PX4 引导程序更新 {#bootloader}

该板预装了[Betaflight](https://github.com/betaflight/betaflight/wiki)。
在安装PX4固件前，必须先烧录_PX4引导程序_。
下载 [holybro_kakuteh7-wing.hex](https://github.com/PX4/PX4-Autopilot/raw/main/docs/assets/flight_controller/kakuteh7-wing/holybro_kakuteh7-wing_bootloader.hex) 引导程序二进制文件，并阅读[此页面](../advanced_config/bootloader_update_from_betaflight.md)获取烧录说明。

## 固件编译

为本目标[编译PX4](../dev_setup/building_px4.md)：

```
make holybro_kakuteh7-wing_default
```

## 安装PX4固件

::: info
KakuteH7-wing 在 PX4 v1.16 或更高版本中受支持。
在该版本之前，需要手动编译并安装固件。
:::

固件可通过以下任一常规方式手动安装：

- 编译并上传源代码：

  ```
  make holybro_kakuteh7-wing_default upload
  ```

- 使用 _QGroundControl_ [加载固件](../config/firmware.md)。
  可使用预构建固件或自定义固件。

## 串口映射

| UART   | 设备       | 接口                  | 默认功能   |
| ------ | ---------- | --------------------- | ---------- |
| USART1 | /dev/ttyS0 | GPS 1                 | GPS1       |
| USART2 | /dev/ttyS1 | R2, T2                | GPS2       |
| USART3 | /dev/ttyS2 | R3, T3                | TELEM1     |
| UART5  | /dev/ttyS3 | R5, T5                | TELEM2     |
| USART6 | /dev/ttyS4 | R6, (T6)              | RC 输入    |
| UART7  | /dev/ttyS5 | R7, T7, RTS, CTS      | TELEM3     |
| UART8  | /dev/ttyS6 | R8, T8                | Console    |

## 调试端口

### 系统控制台

UART8 RX 和 TX 被配置为 [系统控制台](../debug/system_console.md) 使用。