# PilotPi 与 Ubuntu Server

:::warning
Ubuntu Server 在 RPi 4B 上会消耗大量电流并产生大量热量。
使用此硬件时需设计更好的散热方案并考虑高功耗。
:::

## 开发者快速入门

### OS 镜像

支持 armhf 和 arm64 架构。

#### armhf

- [Ubuntu Server 18.04.5（适用于 RPi2）](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi2.img.xz)
- [Ubuntu Server 18.04.5（适用于 RPi3）](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi3.img.xz)
- [Ubuntu Server 18.04.5（适用于 RPi4）](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-armhf+raspi4.img.xz)
- [Ubuntu Server 20.04.1（适用于 RPi 2/3/4）](https://cdimage.ubuntu.com/releases/20.04.1/release/ubuntu-20.04.2-preinstalled-server-arm64+raspi.img.xz)

#### arm64

- [Ubuntu Server 18.04.5（适用于 RPi3）](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-arm64+raspi3.img.xz)
- [Ubuntu Server 18.04.5（适用于 RPi4）](https://cdimage.ubuntu.com/releases/18.04.5/release/ubuntu-18.04.5-preinstalled-server-arm64+raspi4.img.xz)
- [Ubuntu Server 20.04.1（适用于 RPi 3/4）](https://cdimage.ubuntu.com/releases/20.04.1/release/ubuntu-20.04.2-preinstalled-server-arm64+raspi.img.xz)

#### 最新 OS

请参考官方 [cdimage](https://cdimage.ubuntu.com/releases/) 页面获取更新。

### 首次启动

首次设置 RPi 的 WiFi 时，建议使用家用路由器与 RPi 之间的有线以太网连接，并配备显示器和键盘。

#### 启动前

将 SD 卡安装到计算机上并修改网络设置。  
请按照官方指南 [此处](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-wifi-or-ethernet) 操作。

现在将 SD 卡插入您的 Pi 并首次启动。  
确保您可以通过有线以太网的 SSH 连接或直接使用键盘和显示器访问 RPi。

#### WiFi 区域

首先安装所需软件包：

```sh
sudo apt-get install crda
```

编辑文件 `/etc/default/crda` 以设置正确的 WiFi 区域。[参考列表](https://www.arubanetworks.com/techdocs/InstantWenger_Mobile/Advanced/Content/Instant%20User%20Guide%20-%20volumes/Country_Codes_List.htm)

```sh
sudo nano /etc/default/cra
```

重启后，您的 Pi 将能够加入 WiFi 网络。

#### 主机名和 mDNS

首先设置主机名：

```sh
sudo nano /etc/hostname
```

将主机名更改为任意您喜欢的名称。  
然后安装 mDNS 所需的软件包：

```sh
sudo apt-get update
sudo apt-get install avahi-daemon
```

执行重启：

```sh
sudo reboot
```

在上述操作后，通过 WiFi 连接恢复访问。

```sh
ssh ubuntu@pi_hostname.local
```

#### 无密码认证（可选）

您也可以设置 [无密码认证](https://www.raspberrypi.org/documentation/remote-access/ssh/passwordless.md)。

### 操作系统设置

#### config.txt

Ubuntu 中对应的文件为 `/boot/firmware/usercfg.txt`。

```sh
sudo nano /boot/firmware/usercfg.txt
```

用以下内容替换文件：

```sh

```# 启用 sc16is752 覆盖层  
dtoverlay=sc16is752-spi1# 启用 I2C-1 并将频率设置为 400KHz
dtparam=i2c_arm=on,i2c_arm_baudrate=400000# 启用 spidev0.0
dtparam=spi=on# 启用RC输入
enable_uart=1# 启用 I2C-0
dtparam=i2c_vc=on# 切换蓝牙至 miniuart
dtoverlay=miniuart-bt
```

#### cmdline.txt

在Ubuntu Server 20.04中：

```sh
sudo nano /boot/firmware/cmdline.txt
```

在Ubuntu Server 18.04或更早版本中，需要同时修改`nobtcmd.txt`和`btcmd.txt`：

```sh
sudo nano /boot/firmware/nobtcmd.txt
```

找到`console=/dev/ttyAMA0,115200`并删除该部分以禁用串口登录外壳。

在最后一词后追加`isolcpus=2`。
文件最终内容应如下：

```sh
net.ifnames=0 dwc_otg.lpm_enable=0 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait fixrtc isolcpus=2
```

上述配置指示Linux内核不要在CPU核心2上调度任何进程。
稍后我们将手动将PX4运行在该核心上。

重启并SSH连接到你的树莓派。

检查串口接口：

```sh
ls /dev/tty*
```

应包含`/dev/ttyAMA0`、`/dev/ttySC0`和`/dev/ttySC1`。

检查I2C接口：

```sh
ls /dev/i2c*
```

应包含`/dev/i2c-0`和`/dev/i2c-1`

检查SPI接口：

```sh
ls /dev/spidev*
```

应包含`/dev/spidev0.0`。

#### rc.local

在本节中我们将配置**rc.local**的自动启动脚本。
注意需要创建该文件，因为全新安装的Ubuntu系统中默认不存在。

```sh
sudo nano /etc/rc.local
```

向文件追加以下内容：

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
然后设置正确权限：

```sh
sudo chmod +x /etc/rc.local
```

::: info
在不需要时记得关闭该开关！
:::

#### CSI相机

:::warning
启用CSI相机将停止I2C-0上的所有操作。
:::

```sh
sudo nano /boot/firmware/usercfg.txt
```

在文件末尾追加以下行：

```sh
start_x=1
```

### 编译代码

要获取最新版本到计算机上，请在终端中输入以下命令：

```sh
git clone https://github.com/PX4/PX4-Autopilot.git --recursive
```

::: info
仅需执行上述步骤即可完成最新代码的编译。
:::

#### 设置RPi上传目标

使用以下命令设置RPi的IP（或主机名）：

```sh
export AUTOPILOT_HOST=192.168.X.X
```

或

```sh
export AUTOPILOT_HOST=pi_hostname.local
```

此外，需要设置用户名：

```sh
export AUTOPILOT_USER=ubuntu
```

#### 为armhf目标编译

编译可执行文件：

```sh
cd Firmware
make scumaker_pilotpi_default
```

然后上传：

```sh
make scumaker_pilotpi_default upload
```

#### 为armhf目标的替代编译方法（使用docker）

如果是首次使用docker编译，请参考[官方文档](../test_and_ci/docker.md#prerequisites)。

在firmware文件夹中执行命令：

```sh
./Tools/docker_run.sh "export AUTOPILOT_HOST=192.168.X.X; export AUTOPILOT_USER=ubuntu; export NO_NINJA_BUILD=1; make scumaker_pilotpi_default upload"
```

::: info
docker不支持mDNS。上传时必须每次指定正确的IP地址。
:::

::: info
如果IDE不支持ninja构建，`NO_NINJA_BUILD=1`选项将有所帮助。
也可以不上传直接编译，只需移除`upload`目标。
:::

也可以仅通过命令编译代码：

```sh
./Tools/docker_run.sh "make scumaker_pilotpi_default"
```

#### 为arm64目标编译

::: info
此步骤需要安装`aarch64-linux-gnu`工具链。
:::

编译可执行文件：

```sh
cd PX4-Autopilot
make scumaker_pilotpi_arm64
```

然后上传：

```sh
make scumaker_pilotpi_arm64 upload
```

#### 为arm64目标的替代编译方法（使用docker）

如果是首次使用docker编译，请参考[官方文档](../test_and_ci/docker.md#prerequisites)。

在`PX4-Autopilot`文件夹中执行命令：

```sh
./Tools/docker_run.sh "export AUTOPILOT_HOST=192.168.X.X; export AUTOPILOT_USER=ubuntu; export NO_NINJA_BUILD=1; make scumaker_pilotpi_arm64 upload"
```

::: info
docker不支持mDNS。上传时必须每次指定正确的IP地址。
:::

::: info
如果IDE不支持ninja构建，`NO_NINJA_BUILD=1`选项将有所帮助。
也可以不上传直接编译，只需移除`upload`目标。
:::

也可以仅通过命令编译代码：

```sh
./Tools/docker_run.sh "make scumaker_pilotpi_arm64"
```

#### 手动运行PX4

通过SSH连接并运行：

```sh
cd px4
sudo taskset -c 2 ./bin/px4 -s pilotpi_mc.config
```

现在PX4已使用多旋翼配置启动。

如果你的Pi上执行`bin/px4`时遇到类似以下错误：

```
bin/px4: /lib/xxxx/xxxx: version `GLIBC_2.29' not found (required by bin/px4)
```

则应改用docker进行编译。

在进行下一步之前，先清除现有编译：

```sh
rm -rf build
```

然后重新编译。

#### 后续步骤

完成编译和部署后，建议检查系统日志以确保没有错误：

```sh
dmesg | tail -20
```

同时可以使用以下命令监控PX4的运行状态：

```sh
tail -f /home/ubuntu/px4/px4.log
```

::: info
详细调试信息可通过添加`--loglevel=debug`参数获取。
:::

#### 验证部署

通过以下命令验证服务状态：

```sh
systemctl status px4
```

如果服务未启动，可以使用以下命令手动启动：

```sh
sudo systemctl start px4
```

并设置开机自启：

```sh
sudo systemctl enable px4
```

::: warning
如果遇到硬件兼容性问题，请参考官方硬件兼容性列表。
:::

#### 故障排查

常见问题排查步骤：

1. 检查硬件连接
2. 验证内核版本：
   ```sh
   uname -r
   ```
3. 检查依赖项：
   ```sh
   sudo apt-get install -f
   ```
4. 查看系统日志：
   ```sh
   journalctl -u px4.service
   ```

::: info
完整故障排查指南请参考[官方文档](https://docs.px4.io/master/en/troubleshooting/)
:::

#### 系统优化

建议执行以下优化操作：

```sh
sudo apt-get update && sudo apt-get upgrade -y
```

并调整系统内存分配：

```sh
sudo nano /etc/default/grub
# 修改GRUB_CMDLINE_LINUX="... cgroup_enable=memory"
sudo update-grub
```

::: warning
修改内核参数前请确保了解相关风险。
:::

#### 更新系统

定期使用以下命令更新系统：

```sh
cd PX4-Autopilot
git pull
make scumaker_pilotpi_default
make scumaker_pilotpi_default upload
```

::: info
建议每周执行一次系统更新以保持最新安全补丁。
:::