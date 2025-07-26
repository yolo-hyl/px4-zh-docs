

# PilotPi 与 Ubuntu Server

:::warning
Ubuntu Server 在 RPi 4B 上会消耗大量电流并产生大量热量。
在使用此硬件时，应设计更好的散热方案并考虑高功耗。
:::

## 开发者快速入门

### 操作系统镜像

同时支持 armhf 和 arm64 架构。

#### armhf

- [Ubuntu Server 18.04.5适用于RPi2](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi2.img.xz)
- [Ubuntu Server 18.04.5适用于RPi3](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi3.img.xz)
- [Ubuntu Server 18.04.5适用于RPi4](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi4.img.xz)
- [Ubuntu Server 20.04.1适用于RPi 2/3/4](https://cdimage.ubuntu.com/releases/20.04.1/release/ubuntu-20.04.2-preinstalled-server-arm64+raspi.img.xz)

#### arm64

- [Ubuntu Server 18.04.5 for RPi3](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-arm64+raspi3.img.xz)
- [Ubuntu Server 18.04.5 for RPi4](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-arm64+raspi4.img.xz)
- [Ubuntu Server 20.04.1 for RPi 3/4](https://cdimage.ubuntu.com/releases/20.04.1/release/ubuntu-20.04.2-preinstalled-server-arm64+raspi.img.xz)

#### 最新操作系统

请参考官方 [cdimage](https://cdimage.ubuntu.com/releases/) 页面获取最新更新。

### 首次启动

当首次设置 RPi 的 WiFi 时，我们建议在您的家庭路由器和 RPi 之间使用有线以太网连接，并连接显示器和键盘。

#### 启动前

将SD卡安装到计算机上并修改网络设置。  
请按照[此处](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-wifi-or-ethernet)的官方说明进行操作。

现在将SD卡插入Pi并首次启动。  
确保您拥有RPi的shell访问权限——可以通过有线以太网的SSH连接，或者通过键盘和显示器直接访问。

#### WiFi区域

首先安装所需软件包：

```sh
sudo apt-get install crda
```

编辑文件`/etc/default/crda`以设置正确的WiFi区域。[参考列表](https://www.arubanetworks.com/techdocs/InstantWenger_Mobile/Advanced/Content/Instant%20User%20Guide%20-%20volumes/Country_Codes_List.htm)

```sh
sudo nano /etc/default/crda
```

之后你的Pi在重启后将能够连接到你的WiFi网络。

#### 主机名和mDNS

首先设置主机名。

```sh
sudo nano /etc/hostname
```

将主机名更改为你喜欢的名称。
然后安装mDNS所需的软件包：

```sh
sudo apt-get update
sudo apt-get install avahi-daemon
```

执行重启。

```sh
sudo reboot
```

通过WiFi连接重新访问设备。

```sh
ssh ubuntu@pi_hostname.local
```

#### 免密码认证（可选）

您可能也希望设置[免密码认证](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)。

### 设置操作系统

#### config.txt

Ubuntu 中对应的文件为 `/boot/firmware/usercfg.txt`。

```sh
sudo nano /boot/firmware/usercfg.txt
```

将文件内容替换为： 

```sh

```

# 启用 sc16is752 overlay
dtoverlay=sc16is752-spi1

# 启用 I2C-1 并将频率设置为 400KHz
dtparam=i2c_arm=on,i2c_arm_baudrate=400000

# 启用 spidev0.0
dtparam=spi=on

# 启用RC输入
enable_uart=1

# 启用 I2C-0
dtparam=i2c_vc=on

# 切换蓝牙至miniuart
dtoverlay=miniuart-bt

#### cmdline.txt

在 Ubuntu Server 20.04 上：

```sh
sudo nano /boot/firmware/cmdline.txt
```

在 Ubuntu Server 18.04 或更早版本上，`nobtcmd.txt` 和 `btcmd.txt` 都需要修改。

```sh
sudo nano /boot/firmware/nobtcmd.txt
```

找到 `console=/dev/ttyAMA0,115200` 并删除该部分以禁用串口登录shell。

在最后一词后追加 `isolcpus=2`。  
修改后的完整文件应如下：

```sh
net.ifnames=0 dwc_otg.lpm_enable=0 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait fixrtc isolcpus=2
```

上述配置指示 Linux 内核不要在 CPU 2 核心上调度任何进程。  
我们将在后续手动将 PX4 运行到该核心上。

重启设备并通过 SSH 登录到你的 Raspberry Pi。

检查 UART 接口：

```sh
ls /dev/tty*
```

应存在 `/dev/ttyAMA0`、`/dev/ttySC0` 和 `/dev/ttySC1`。

检查 I2C 接口：

```sh
ls /dev/i2c*
```

应存在 `/dev/i2c-0` 和 `/dev/i2c-1`。

检查 SPI 接口：

```sh
ls /dev/spidev*
```

应存在 `/dev/spidev0.0`。

#### rc.local

在本节中，我们将配置 **rc.local** 中的自动启动脚本。  
请注意，我们需要创建此文件，因为它在全新安装的Ubuntu系统中不存在。

```sh
sudo nano /etc/rc.local
```

将以下内容追加到文件中：

```sh
#!/bin/sh

echo "25" > /sys/class/gpio/export
echo "in" > /sys/class/gpio/gpio25/direction
if [ $(cat /sys/class/gpio/gpio25/value) -eq 1 ] ; then
        echo "Launching PX4"
        cd /home/ubuntu/px4 ; nohup taskset -c 2 ./bin/px4 -d -s pilotpi_mc.config 2 &> 1 > /home/ubuntu/px4/px4.log &
fi
echo "25" > /sys/class/gpio/unexport

exit 0
```

保存并退出。  
然后设置正确的权限：

```sh
sudo chmod +x /etc/rc.local
```

::: info
不需要时请务必关闭开关！
:::

#### CSI摄像头

:::warning
启用CSI摄像头将停止I2C-0上所有功能的运行。
:::

```sh
sudo nano /boot/firmware/usercfg.txt
```

在文件末尾添加以下行：

```sh
start_x=1
```

### 构建代码

要将最新版本代码下载到计算机上，请在终端中输入以下命令：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

::: info
这一步就足够完成最新代码的构建。
:::

#### 设置 RPi 上传目标

使用以下命令设置 RPi 的 IP（或主机名）：

```sh
export AUTOPILOT_HOST=192.168.X.X
```

或

```sh
export AUTOPILOT_HOST=pi_hostname.local
```

此外，我们需要设置用户名：

```sh
export AUTOPILOT_USER=ubuntu
```

#### 为 armhf 目标构建

构建可执行文件：

```sh
cd Firmware
make scumaker_pilotpi_default
```

然后通过以下命令上传：

```sh
make scumaker_pilotpi_default upload
```

#### armhf 的替代构建方法（使用 docker）

如果这是您首次使用 docker 进行编译，请参考 [官方文档](../test_and_ci/docker.md#prerequisites)。

在 firmware 目录中执行以下命令：

```sh
./Tools/docker_run.sh "export AUTOPILOT_HOST=192.168.X.X; export AUTOPILOT_USER=ubuntu; export NO_NINJA_BUILD=1; make scumaker_pilotpi_default upload"
```

::: info
mDNS 不支持在 docker 中使用。每次上传代码时都必须指定正确的 IP 地址。
:::

::: info
如果您的 IDE 不支持 ninja 构建，`NO_NINJA_BUILD=1` 选项将有所帮助。
您也可以不上传代码就进行编译，只需移除 `upload` 目标即可。
:::

您也可以仅通过以下命令编译代码：

```sh
./Tools/docker_run.sh "make scumaker_pilotpi_default"
```

#### 为 arm64 目标构建

::: info
此步骤要求安装 `aarch64-linux-gnu` 工具链。
:::

构建可执行文件：

```sh
cd PX4-Autopilot
make scumaker_pilotpi_arm64
```

然后使用以下命令上传：

```sh
make scumaker_pilotpi_arm64 upload
```

#### arm64 的另一种构建方法（使用 docker）

如果这是您第一次使用 docker 进行编译，请参考 [官方文档](../test_and_ci/docker.md#prerequisites)。

在 `PX4-Autopilot` 文件夹中执行以下命令：

```sh
./Tools/docker_run.sh "export AUTOPILOT_HOST=192.168.X.X; export AUTOPILOT_USER=ubuntu; export NO_NINJA_BUILD=1; make scumaker_pilotpi_arm64 upload"
```

::: info
docker 中不支持 mDNS。每次上传时都必须指定正确的 IP 地址。
:::

::: info
如果您的 IDE 不支持 ninja 构建，则 `NO_NINJA_BUILD=1` 选项将有所帮助。
您也可以在不上传的情况下进行编译 - 只需删除 `upload` 目标。
:::

您也可以仅通过以下命令进行编译：

```sh
./Tools/docker_run.sh "make scumaker_pilotpi_arm64"
```

#### 手动运行 PX4

通过 SSH 连接并运行：

```sh
cd px4
sudo taskset -c 2 ./bin/px4 -s pilotpi_mc.config
```

现在 PX4 已使用多旋翼配置启动。

如果你在 Pi 上执行 `bin/px4` 时遇到类似以下问题：

```
bin/px4: /lib/xxxx/xxxx: version `GLIBC_2.29' not found (required by bin/px4)
```

那么你应该改用 Docker 进行编译。

在进行下一步之前，首先清除现有的构建：

```sh
rm -rf build/scumaker_pilotpi_*
```

然后返回到上面的相应章节。

### 配置后

请参考[此处](raspberry_pi_pilotpi_rpios.md)的说明