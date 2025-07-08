# SiK Radio 集成

[SiK radio](https://github.com/LorenzMeier/SiK) 是一个包含遥测无线电固件和工具的集合。

关于 _使用_ SiK Radio 的信息请参考 [外设硬件 > 遥测 > SiK Radio](../telemetry/sik_radio.md)

以下("开发者")信息说明如何从源码构建 SiK 固件以及如何使用 AT 命令进行配置。

## 支持的无线电硬件

SiK 代码库包含以下遥测无线电的引导程序和固件(2020-02-25)：

- HopeRF HM-TRP
- HopeRF RF50-DEMO
- RFD900
- RFD900a
- RFD900p
- RFD900pe
- RFD900u
- RFD900ue

::: info
SiK 代码库目前不包含 RFD900x 或 RFD900ux 遥测无线电的固件。
为了更新这些无线电的固件(例如支持 MAVLink v2.0)，建议采用以下流程：

1. 从 [RFDesign 网站](https://files.rfdesign.com.au/firmware/) 下载对应固件
1. 在 Windows 电脑上下载并安装 [RFD Modem Tools](https://files.rfdesign.com.au/tools/)
1. 使用 RFD Modem Tools GUI 将固件上传到您的 RFD900x 或 RFD900ux 遥测无线电

:::

## 构建说明

您需要安装所需的 8051 编译器，因为默认 PX4 构建工具链不包含该编译器。

### Mac OS

安装工具链：

```sh
brew install sdcc
```

构建标准 SiK Radio / 3DR Radio 的镜像：

```sh
git clone https://github.com/LorenzMeier/SiK.git
cd SiK/Firmware
make install
```

上传到无线电(**修改串口名称**)：

```
tools/uploader.py --port /dev/tty.usbserial-CHANGETHIS dst/radio~hm_trp.ihx
```

## 配置说明

无线电支持 AT 命令进行配置。

```sh
screen /dev/tty.usbserial-CHANGETHIS 57600 8N1
```

然后启动命令模式：

::: info
请勿在前后一秒内输入任何内容
:::

```sh
+++
```

列出当前设置：

```sh
ATI5
```

然后设置网络 ID、写入设置并重启无线电：

```sh
ATS3=55
AT&W
ATZ
```

::: info
您可能需要重新上电无线电才能连接第二个无线电。
:::