

# PilotPi 与 Raspberry Pi OS

## 开发者快速入门

### 操作系统镜像

建议始终使用最新的官方[Raspberry Pi OS Lite](https://downloads.raspberrypi.org/raspios_lite_armhf_latest)镜像。

安装前必须已具备与RPi的SSH连接。

### 设置访问权限（可选）

#### 主机名和 mDNS

mDNS 可帮助您通过主机名而非 IP 地址连接到 RPi。

```sh
sudo raspi-config
```

导航至 **网络选项 > 主机名**。
设置并退出。
您可能还需要配置 [无需密码的认证](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)。

### 设置操作系统

#### config.txt

```sh
sudo nano /boot/config.txt
```

用以下内容替换文件：

```sh

```

# 启用 sc16is752 覆盖层
dtoverlay=sc16is752-spi1

# 启用 I2C-1 并将频率设置为 400千赫兹
dtparam=i2c_arm=on,i2c_arm_baudrate=400000

# 启用 spidev0.0
dtparam=spi=on

# 启用RC输入
enable_uart=1

# 启用 I2C-0
dtparam=i2c_vc=on

# 切换蓝牙到miniuart
dtoverlay=miniuart-bt

#### cmdline.txt

```sh
sudo raspi-config
```

**接口选项 > 串口 > 登录外壳 = 否 > 硬件 = 是**.
启用UART但不带登录外壳。

```sh
sudo nano /boot/cmdline.txt
```

在最后一个单词后追加 `isolcpus=2`。
整个文件内容应为：

```sh
console=tty1 root=PARTUUID=xxxxxxxx-xx rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait isolcpus=2
```

这告诉Linux内核不要在CPU核心2上调度任何进程。
我们稍后会手动将PX4运行在该核心上。

重启并SSH连接到你的RPi。

检查UART接口：

```sh
ls /dev/tty*
```

应该有 `/dev/ttyAMA0`、`/dev/ttySC0` 和 `/dev/ttySC1`。

检查I2C接口：

```sh
ls /dev/i2c*
```

应该有 `/dev/i2c-0` 和 `/dev/i2c-1`。

检查SPI接口：

```sh
ls /dev/spidev*
```

应该有 `/dev/spidev0.0`。

#### rc.local

在本节中，我们将配置 **rc.local** 中的自动启动脚本。

```sh
sudo nano /etc/rc.local
```

将以下内容添加到上述文件的 `exit 0` 之前：

```sh
echo "25" > /sys/class/gpio/export
echo "in" > /sys/class/gpio/gpio25/direction
if [ $(cat /sys/class/gpio/gpio25/value) -eq 1 ] ; then
        echo "Launching PX4"
        cd /home/pi/px4 ; nohup taskset -c 2 ./bin/px4 -d -s pilotpi_mc.config 2 &> 1 > /home/pi/px4/px4.log &
fi
echo "25" > /sys/class/gpio/unexport
```

保存并退出。

::: info
不需要时请记得关闭开关。
:::

#### CSI摄像头

::: info
启用CSI摄像头将停止I2C-0上的任何工作。
:::

```sh
sudo raspi-config
```

**接口选项 > 摄像头**

### 构建代码

要将最新版本代码克隆到计算机上，请在终端中输入以下命令：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

::: info
如果您只需要构建最新代码，仅需执行以上操作即可。
:::

#### 为 Raspberry Pi OS 交叉编译

使用以下方式设置你的 RPi 的 IP（或主机名）：

```sh
export AUTOPILOT_HOST=192.168.X.X
```

或

```sh
export AUTOPILOT_HOST=pi_hostname.local
```

构建可执行文件：

```sh
cd PX4-Autopilot
make scumaker_pilotpi_default
```

然后通过以下命令上传：

```sh
make scumaker_pilotpi_default upload
```

通过 ssh 连接并运行：

```sh
cd px4
sudo taskset -c 2 ./bin/px4 -s pilotpi_mc.config
```

此时 PX4 将以多旋翼配置启动。

如果你在 Pi 上执行 `bin/px4` 时遇到类似以下问题：

```
bin/px4: /lib/xxxx/xxxx: version `GLIBC_2.29' not found (required by bin/px4)
```

则应改用 docker 进行编译。

在进行下一步之前，请先清除现有的编译内容：

```sh
rm -rf build/scumaker_pilotpi_default
```

### 替代构建方法（使用Docker）

以下方法可提供与CI中部署相同的工具集。

如果您是首次使用Docker进行编译，请参考[官方文档](../test_and_ci/docker.md#prerequisites)。

在PX4-Autopilot目录下执行命令：

```sh
./Tools/docker_run.sh "export AUTOPILOT_HOST=192.168.X.X; export NO_NINJA_BUILD=1; make scumaker_pilotpi_default upload"
```

::: info
docker中不支持mDNS。上传时必须每次指定正确的IP地址。
:::

::: info
如果您的IDE不支持ninja构建，`NO_NINJA_BUILD=1`选项将有所帮助。
您也可以不上传直接编译。只需移除`upload`目标。
:::

也可以通过以下命令仅编译代码：

```sh
./Tools/docker_run.sh "make scumaker_pilotpi_default"
```

### 配置后

您需要检查这些额外项目以确保机体正常工作。

#### 执行器配置

首先为您的机体设置 [CA_AIRFRAME](../advanced_config/parameter_reference.md#CA_AIRFRAME) 参数。

然后您可以使用正常的 [执行器配置](../config/actuators.md) 配置界面分配输出（一个输出标签将出现在RPi PWM输出驱动程序中）。

#### 外部指南针

在启动脚本(`*.config`)中，您可以找到

```sh

```

# 外部GPS & 电子罗盘
gps start -d /dev/ttySC0 -i uart -p ubx -s
#hmc5883 start -X
#ist8310 start -X

```

根据你的具体情况取消相应的注释。
不确定你的GPS模块搭配的是哪种电子罗盘？执行以下命令并查看输出：

```sh
sudo apt-get update
sudo apt-get install i2c-tools
i2cdetect -y 0
```

示例输出：

```
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- 0e --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- 1e --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- --
```

`1e` 表示基于HMC5883的电子罗盘已安装在外部I2C总线上。同样，IST8310的值为 `0e`。

::: info
通常你只会有一个。
如果其他设备连接到外部I2C总线(地址为`/dev/i2c-0`)，它们也会显示在这里。
:::