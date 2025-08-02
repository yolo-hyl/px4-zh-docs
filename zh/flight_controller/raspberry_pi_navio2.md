

# Raspberry Pi 2/3/4 Navio2 自动驾驶仪

<LinkedBadge type="warning" text="实验性" url="../flight_controller/autopilot_experimental.html"/>

:::warning
PX4 不制造此（或任何）自动驾驶仪。
如有硬件支持或合规性问题，请联系[制造商](https://emlid.com/)。
:::

这是 Raspberry Pi 2/3/4 Navio2 自动驾驶仪的开发者"快速入门"指南。
它允许您构建 PX4 并将其传输到 RPi，或进行本地构建。

![Ra Pi Image](../../assets/hardware/hardware-rpi2.jpg)

## OS镜像

使用预配置的[Emlid Raspberry Pi OS镜像（适用于Navio 2）](https://docs.emlid.com/navio2/configuring-raspberry-pi)。  
默认镜像将包含下文所示的大部分设置步骤。

:::warning
请勿升级系统（更具体地说是内核）。  
升级可能导致安装缺少必要硬件支持的新内核（可通过`ls /sys/class/pwm`验证，该目录不应为空）。
:::

## 设置访问

Raspberry Pi OS 镜像已预先配置了SSH。
用户名为"pi"，密码为"raspberry"。
本指南假设用户名和密码保持默认设置。

要将Pi设置为连接到本地Wi-Fi，请遵循[此指南](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)，或通过以太网线连接。

要通过SSH连接到您的Pi，请使用默认用户名(`pi`)和主机名(`navio`)。
或者（如果此方法不可行），您可以找到RPi的IP地址并进行指定。

```sh
ssh pi@navio.local
```

or

```sh
ssh pi@<IP-ADDRESS>
```

## 扩展文件系统

通过运行以下命令来扩展文件系统以充分利用整个SD卡的存储空间：

```sh
sudo raspi-config --expand-rootfs
```

## 禁用 Navio RGB 覆盖层

现有的 Navio RGB 覆盖层会占用 PX4 用于 RGB 灯的通用输入输出引脚(GPIOs)。
通过编辑 `/boot/config.txt` 文件并注释启用 `navio-rgb` 覆盖层的行来实现禁用。

```
#dtoverlay=navio-rgb
```

## 测试文件传输

我们使用 SCP 通过网络（WiFi 或以太网）将文件从开发计算机传输到目标板。

要测试您的配置，请尝试现在通过网络将文件从开发 PC 传输到 Pi。
请确保 Pi 具有网络访问权限，并且您可以通过 SSH 登录到它。

```sh
echo "Hello" > hello.txt
scp hello.txt pi@navio.local:/home/pi/
rm hello.txt
```

这应该会将一个 "hello.txt" 文件复制到您的 Pi 的主文件夹中。
验证文件确实已复制，然后可以继续下一步。

## PX4 开发环境

这些说明解释如何在Ubuntu 18.04上安装PX4开发环境以构建RPi。

::: 警告
Navio 2的PX4二进制文件只能在Ubuntu 18.04上运行。

你可以使用GCC工具链在Ubuntu 20.04上构建PX4，但生成的二进制文件版本过新，无法在实际的Pi上运行（截至2023年9月）。
关于更多信息，请参见[PilotPi与Raspberry Pi OS开发者快速入门 > 使用Docker的替代构建方法](../flight_controller/raspberry_pi_pilotpi_rpios.md#alternative-build-method-using-docker)。
:::

### 安装通用依赖项

要获取 Raspberry Pi 的通用依赖项：

1. 从 PX4 源代码仓库 (**/Tools/setup/**) 下载 [ubuntu.sh](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/ubuntu.sh) <!-- NEED px4_version --> 和 [requirements.txt](https://github.com/PX4/PX4-Autopilot/blob/main/Tools/setup/requirements.txt) 文件： <!-- NEED px4_version -->

   ```sh
   wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/ubuntu.sh
   wget https://raw.githubusercontent.com/PX4/PX4-Autopilot/main/Tools/setup/requirements.txt
   ```

1. 在终端中运行 **ubuntu.sh** 仅获取通用依赖项：

   ```sh
   bash ubuntu.sh --no-nuttx --no-sim-tools
   ```

1. 然后按照后续章节所述设置交叉编译器（GCC 或 clang）。

### GCC (armhf)

Ubuntu软件仓库提供了一组预编译工具链。请注意Ubuntu Focal默认安装的`gcc-9-arm-linux-gnueabihf`不完全受支持，因此我们必须手动安装`gcc-8-arm-linux-gnueabihf`并将其设置为默认工具链。本指南同样适用于早期Ubuntu版本（Bionic）。
以下说明假设您尚未安装任何版本的arm-linux-gnueabihf，并将通过`update-alternatives`设置默认执行文件。
通过终端命令安装：

```sh
sudo apt-get install -y gcc-8-arm-linux-gnueabihf g++-8-arm-linux-gnueabihf
```

设置为默认工具链：

```sh
sudo update-alternatives --install /usr/bin/arm-linux-gnueabihf-gcc arm-linux-gnueabihf-gcc /usr/bin/arm-linux-gnueabihf-gcc-8 100 --slave /usr/bin/arm-linux-gnueabihf-g++ arm-linux-gnueabihf-g++ /usr/bin/arm-linux-gnueabihf-g++-8
sudo update-alternatives --config arm-linux-gnueabihf-gcc
```

### GCC (aarch64)

如果需要为ARM64设备构建PX4，则需要本节内容。

```sh
sudo apt-get install -y gcc-8-aarch64-linux-gnu g++-8-aarch64-linux-gnu
sudo update-alternatives --install /usr/bin/aarch64-linux-gnu-gcc aarch64-linux-gnu-gcc /usr/bin/aarch64-linux-gnu-gcc-8 100 --slave /usr/bin/aarch64-linux-gnu-g++ aarch64-linux-gnu-g++ /usr/bin/aarch64-linux-gnu-g++-8
sudo update-alternatives --config aarch64-linux-gnu-gcc
```

### Clang（可选）

首先安装GCC（使用clang需要此工具）。

我们建议您从Ubuntu软件仓库获取clang，如下所示：

```sh
sudo apt-get install clang
```

以下示例展示如何使用_CMake_在树外构建PX4固件。

```sh
cd <PATH-TO-PX4-SRC>
mkdir build/px4_raspberrypi_default_clang
cd build/px4_raspberrypi_default_clang
cmake \
-G"Unix Makefiles" \
-DCONFIG=px4_raspberrypi_default \
-UCMAKE_C_COMPILER \
-DCMAKE_C_COMPILER=clang \
-UCMAKE_CXX_COMPILER \
-DCMAKE_CXX_COMPILER=clang++ \
../..
make
```

## 构建代码

使用以下命令指定你的树莓派的IP（或主机名）：

```sh
export AUTOPILOT_HOST=navio.local
```

或

```sh
export AUTOPILOT_HOST=192.168.X.X
```

::: info
在构建之前应设置该环境变量的值，否则`make upload`将无法找到你的树莓派。
:::

在开发机器上构建可执行文件：

```sh
cd PX4-Autopilot
make emlid_navio2
```

可执行文件`px4`位于目录 **build/emlid_navio2_default/** 中。  
确保你可以通过SSH连接到你的树莓派，请按照[Raspberry Pi下armhf的访问说明](#设置访问)查看如何访问你的树莓派的说明。

然后使用以下命令上传：

```sh
cd PX4-Autopilot
make emlid_navio2 upload
```

然后，通过SSH连接并在树莓派上以root身份运行：

```sh
cd ~/px4
sudo ./bin/px4 -s px4.config
```

成功构建并运行PX4后，输出可能如下所示：

```sh

______  __   __    ___
| ___ \ \ \ / /   /   |
| |_/ /  \ V /   / /| |
|  __/   /   \  / /_| |
| |     / /^\ \ \___  |
\_|     \/   \/     |_/

px4正在启动。


pxh>
```

## 自动启动

要实现 PX4 的自动启动，请将以下内容添加到文件 **/etc/rc.local** 中（如果你使用原生构建，请相应调整），添加在 `exit 0` 行之前：

```sh
cd /home/pi && ./bin/px4 -d -s px4.config > px4.log
```