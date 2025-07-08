# PilotPi 与 Raspberry Pi OS

## 开发者快速入门

### 操作系统镜像

始终推荐使用最新的官方 [Raspberry Pi OS Lite](https://downloads.raspberrypi.org/raspios_lite_armhf_latest) 镜像。

要进行安装，您需要已具备与 RPi 的 SSH 连接功能。

### 设置访问 (可选)

#### 主机名和 mDNS

mDNS 可帮助您通过主机名而非 IP 地址连接到 RPi。

```sh
sudo raspi-config
```

导航至 **Network Options > Hostname**。
设置并退出。
您可能还需要配置 [免密码认证](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)。

### 操作系统设置

#### config.txt

```sh
sudo nano /boot/config.txt
```

替换文件内容为：

```sh
# 启用 sc16is752 覆盖层
dtoverlay=sc16is752-spi1
# 启用 I2C-1 并将频率设置为 400KHz
dtparam=i2c_arm=on,i2c_arm_baudrate=400000
# 启用 spidev0.0
dtparam=spi=on
# 启用遥控输入
enable_uart=1
# 启用 I2C-0
dtparam=i2c_vc=on
# 将蓝牙切换到 miniuart
dtoverlay=miniuart-bt
```

#### cmdline.txt

```sh
sudo raspi-config
```

**Interfacing Options > Serial > login shell = No > hardware = Yes**。
启用 UART 但不带登录 shell。

```sh
sudo nano /boot/cmdline.txt
```

在最后一词后追加 `isolcpus=2`。
整个文件内容应为：

```sh
console=tty1 root=PARTUUID=xxxxxxxx-xx rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait isolcpus=2
```

这告诉 Linux 内核不要在 CPU 核心 2 上调度任何进程。
我们稍后会手动在该核心上运行 PX4。

重启并 SSH 登录到您的 RPi。

检查 UART 接口：

```sh
ls /dev/tty*
```

应存在 `/dev/ttyAMA0`、`/dev/ttySC0` 和 `/dev/ttySC1`。

检查 I2C 接口：

```sh
ls /dev/i2c*
```

应存在 `/dev/i2c-0` 和 `/dev/i2c-1`

检查 SPI 接口

```sh
ls /dev/spidev*
```

应存在 `/dev/spidev0.0`。

#### rc.local

在本节中，我们将配置 **rc.local** 中的自动启动脚本。

```sh
sudo nano /etc/rc.local
```

在 `exit 0` 上方追加以下内容：

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
不需要时请务必关闭开关。
:::

#### CSI 摄像头

::: info
启用 CSI 摄像头将停止 I2C-0 上的任何操作。
:::

```sh
sudo raspi-config
```

**Interfacing Options > Camera**

### 编译代码

要在计算机上获取_最新版本_，请在终端中输入以下命令：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

::: info
这只是编译最新代码所需的操作。
:::

#### 为 Raspberry Pi OS 交叉编译

使用以下命令设置您的 RPi IP（或主机名）：

```sh
export AUTOPILOT_HOST=192.168.X.X
```

或

```sh
export AUTOPILOT_HOST=pi_hostname.local
```

编译可执行文件：

```sh
cd PX4-Autopilot
make scumaker_pilotpi_default
```

然后上传：

```sh
make scumaker_pilotpi_default upload
```

通过 ssh 连接并运行：

```sh
cd px4
sudo taskset -c 2 ./bin/px4 -s pilotpi_mc.config
```

现在 PX4 已以多旋翼配置启动。

如果您在 Pi 上执行 `bin/px4` 时遇到类似以下问题：

```
bin/px4: /lib/xxxx/xxxx: version `GLIBC_2.29' not found (required by bin/px4)
```

则应通过 docker 进行编译。

### 通过 Docker 编译

#### 构建 Docker 镜像

```sh
docker build -t px4-dev -f Dockerfile .
```

#### 运行容器

```sh
docker run -it --rm \
  -v ${PWD}:/src \
  -v ${HOME}/.ssh:/root/.ssh \
  -w /src \
  px4-dev
```

#### 在容器内编译

```sh
make scumaker_pilotpi_default
```

#### 从容器上传

```sh
make scumaker_pilotpi_default upload
```

### 后续步骤

1. **验证连接**：确保 RPi 与遥控器/飞控的通信正常。
2. **配置参数**：根据实际硬件调整 PX4 参数（如 `RC_MAP_CHANNEL_1`）。
3. **测试飞行**：在安全环境下进行首次手动飞行测试。

### 常见问题

**Q：上传时提示权限错误？**  
A：确保 RPi 的 USB 接口供电充足，并在主机上运行 `sudo chmod 666 /dev/ttyACM0`。

**Q：启动后无法连接到 PX4？**  
A：检查 `/boot/config.txt` 中的 UART 配置是否启用，重启后重试。

**Q：编译时提示缺少依赖？**  
A：在容器内运行 `apt-get update && apt-get install -y build-essential` 安装基础依赖。

### 参考资料

- [PX4 官方文档](https://docs.px4.io/)
- [Raspberry Pi OS 配置手册](https://www.raspberrypi.com/documentation/)
- [Docker 官方文档](https://docs.docker.com/)