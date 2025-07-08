# Raspberry Pi 2/3/4 Navio2 自动驾驶仪

<LinkedBadge type="warning" text="Experimental" url="../flight_controller/autopilot_experimental.html"/>

:::warning
PX4 不生产此（或任何）自动驾驶仪。  
请联系 [制造商](https://emlid.com/) 以获得硬件支持或合规性问题帮助。
:::

本节是 Raspberry Pi 2/3/4 Navio2 自动驾驶仪的开发者快速入门指南。  
它允许您在 RPi 上构建 PX4 或本机构建。

![Ra Pi Image](../../assets/hardware/hardware-rpi2.jpg)

## 操作系统镜像

使用预配置的 [Emlid Raspberry Pi OS image for Navio 2](https://docs.emlid.com/navio2/configuring-raspberry-pi)。  
默认镜像会包含下方大部分设置步骤。

:::warning
请确保不要升级系统（特别是内核）。  
升级后可能安装缺少必要硬件支持的新内核（可通过 `ls /sys/class/pwm` 检查，目录不应为空）。
:::

## 设置访问权限

Raspberry Pi OS 镜像已配置 SSH。  
用户名为 "pi"，密码为 "raspberry"。  
本指南假设用户名和密码保持默认。

要将 Pi 连接到本地 Wi-Fi，请遵循 [此指南](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)，或通过以太网连接。

通过 SSH 连接到 Pi，使用默认用户名 (`pi`) 和主机名 (`navio`)。  
如果此方法无效，可通过 IP 地址连接。

```sh
ssh pi@navio.local
```

或

```sh
ssh pi@<IP-ADDRESS>
```

## 扩展文件系统

通过运行以下命令扩展文件系统以利用整个 SD 卡：

```sh
sudo raspi-config --expand-rootfs
```

## 禁用 Navio RGB Overlay

现有 Navio RGB Overlay 占用了 PX4 用于 RGB Led 的 GPIO。  
编辑 `/boot/config.txt` 文件，注释掉启用 `navio-rgb` Overlay 的行。

```
#dtoverlay=navio-rgb
```

## 测试文件传输

我们使用 SCP 通过网络（Wi-Fi 或以太网）从开发计算机向目标板传输文件。

要测试您的设置，请尝试现在通过网络将文件从开发 PC 推送到 Pi。  
确保 Pi 有网络访问权限，并且您可以 SSH 进入它。

```sh
echo "Hello" > hello.txt
scp hello.txt pi@navio.local:/home/pi/
rm hello.txt
```

这应该将 "hello.txt" 文件复制到 Pi 的主文件夹中。  
验证文件是否确实被复制，然后继续下一步。

## PX4 开发环境

这些说明解释了如何在 Ubuntu 18.04 上安装 PX4 开发环境以构建 RPi。

::: warning
Navio 2 的 PX4 二进制文件只能在 Ubuntu 18.04 上运行。

您可以在 Ubuntu 20.04 上使用 GCC 工具链构建 PX4，但生成的二进制文件对于实际 Pi 来说过于新（截至 2023 年 9 月）。  
更多信息请参见 [PilotPi with Raspberry Pi OS Developer Quick Start > Alternative build method using docker](../flight_controller/raspberry_pi_pilotpi_rpios.md#alternative-build-method-using-docker)。
:::

### 安装通用依赖项

要获取 Raspberry Pi 的通用依赖项：

1. 从 PX4 源代码仓库下载 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) 和 [requirements.txt](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/

### GCC (armhf)

Ubuntu 软件仓库提供了一组预编译的工具链。注意 Ubuntu Focal 的默认安装是 `gcc-9-arm-linux-gnueabihf`，但不完全支持，因此必须手动安装 `gcc-8-arm-linux-gnueabihf` 并将其设为默认工具链。此指南也适用于早期的 Ubuntu 版本（Bionic）。  
以下说明假设您尚未安装任何版本的 arm-linux-gnueabihf，并将通过使用 `update-alternatives` 设置默认值。  

安装它们使用终端命令：  

```sh
sudo apt-get install -y gcc-8-arm-linux-gnueabihf g++-8-arm-linux-gnueabihf
```

### 交叉编译器设置（armhf）  

如果需要在 x86 系统上为 ARM 编译，请安装交叉编译工具链：  

```sh
sudo apt-get install -y g++-arm-linux-gnueabihf
```

### GCC (aarch64)  

对于 64 位 ARM 系统（如 Raspberry Pi 4），请使用以下命令安装：  

```sh
sudo apt-get install -y gcc-aarch64-linux-gnu g++-aarch64-linux-gnu
```

### Clang 支持  

要使用 Clang，请安装以下依赖项：  

```sh
sudo apt-get install -y clang-10 lld-10
```

## 构建 PX4  

要构建 PX4，请运行以下命令：  

```sh
make px4_sitl_default
```

## 启动 PX4  

要启动 PX4，请运行：  

```sh
./build/px4_sitl_default/bin/px4 -s px4.config
```

## 自动启动  

要设置 PX4 自动启动，请将以下内容添加到 **/etc/rc.local** 文件中（如果使用本机构建，请相应调整路径），在 `exit 0` 行之前：  

```sh
cd /home/pi && ./bin/px4 -d -s px4.config > px4.log
```