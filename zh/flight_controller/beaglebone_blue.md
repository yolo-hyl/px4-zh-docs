# BeagleBone Blue

<LinkedBadge type="warning" text="Experimental" url="../flight_controller/autopilot_experimental.md"/>

:::warning
PX4 不生产该（或任何）自动驾驶仪。
请联系 [制造商](https://beagleboard.org/blue) 获取硬件支持或合规性问题。

:::

[BeagleBone Blue](https://beagleboard.org/blue) 是一款集成了 Linux 系统的多功能计算机。虽然它专为机器人开发优化，但这款紧凑且经济的电路板包含了飞行控制器所需的所有必要传感器和外设。本主题展示如何配置该板以运行 PX4 和 [librobotcontrol](https://github.com/StrawsonDesign/librobotcontrol) 机器人软件包。

![BeagleBone - 标注示意图](../../assets/hardware/BeagleBone_Blue_balloons.jpg)

## 操作系统镜像

_BeagleBone Blue_ 的操作系统镜像可在此处获取：

- [最新的稳定版操作系统镜像](https://beagleboard.org/latest-images)
- [测试版操作系统镜像](https://rcn-ee.net/rootfs/bb.org/testing/)（频繁更新）

有关刷写操作系统镜像的信息请参考 [此页面](https://github.com/beagleboard/beaglebone-blue/wiki/Flashing-firmware)  
其他有用信息请查看 [常见问题解答](<https://github.com/beagleboard/beaglebone-blue/wiki/Frequently-Asked-Questions-(FAQ)>)

:::tip
您可以选择更新到实时内核，如果更新后，请重新检查_librobotcontrol_是否能与实时内核正常工作。
:::

截至本文档更新时的最新操作系统镜像为 [bone-debian-10.3-iot-armhf-2020-04-06-4gb.img.xz](https://debian.beagle.cc/images/bone-debian-10.3-iot-armhf-2020-04-06-4gb.img.xz)

## 交叉编译构建（推荐）

为 _BeagleBone Blue_ 编译 PX4 的推荐方式是：在开发计算机上进行编译，并将 PX4 可执行二进制文件直接上传到 BeagleBone Blue。

:::tip
由于部署速度快且易于使用，此方法比 [原生构建](#native_builds) 更推荐。
:::

::: info
PX4 构建需要 [librobotcontrol](http://strawsondesign.com/docs/librobotcontrol/)，该库已自动包含在构建中（如果需要也可以单独安装和测试）。
:::

### Beaglebone Blue WIFI 设置

为了方便访问您的开发板，可以通过 WiFi 将其连接到家庭网络。

步骤如下（在开发板上执行）：

```sh
sudo su
connmanctl
connmanctl>scan wifi
connmanctl>services
#(此时您应该能看到您的网络 SSID 出现)
connmanctl>agent on
connmanctl>connect <SSID>
	Enter Passphrase
connmanctl>quit
```

::: info
上述 `<SSID>` 的格式通常是文本 'wifi' 后跟一串其他字符。
输入命令后，您将被提示输入 WiFi 密码。
:::

### Beaglebone 的 SSH root 登录

可以通过以下方式启用开发板的 root 登录：

```sh
sudo su
echo "PermitRootLogin yes" >>  /etc/ssh/sshd_config && systemctl restart sshd
```

### 交叉编译设置

1. 首先设置 _rsync_（用于通过网络将文件从开发计算机传输到目标板 - WiFi 或以太网）。
   对于使用 SSH 密钥认证的 _rsync_，请按照以下步骤操作（在开发机器上）：

   1. 如果尚未生成 SSH 密钥，请生成：

      ```
      ssh-keygen -t rsa
      ```

      1. ENTER //无密码短语
      1. ENTER
      1. ENTER

   1. 在 **/etc/hosts** 中将 BeagleBone Blue 板定义为 `beaglebone`，并将公钥复制到板上以实现无密码 SSH 访问：

      ```
      ssh-copy-id debian@beaglebone
      ```

   1. 或者您可以直接使用 beaglebone 的 IP：

      ```
      ssh-copy-id debian@<IP>
      ```

   1. 当提示是否信任时：yes
   1. 输入 root 密码

1. 交叉编译设置

   1. 工具链下载

      1. 首先将工具链安装到 _/opt/bbblue_toolchain/gcc-arm-linux-gnueabihf_。
         以下是一个使用软链接选择所需工具链版本的示例：

         ```sh
         mkdir -p /opt/bbblue_toolchain/gcc-arm-linux-gnueabihf
         chmod -R 777 /opt/bbblue_toolchain
         cd /opt/bbblue_toolchain/gcc-arm-linux-gnueabihf
         ```

         _BeagleBone Blue_ 的 ARM 交叉编译器可在 [Linaro 工具链二进制文件网站](https://www.linaro.org/downloads/#gnu_and_llvm) 找到。

         :::tip
         工具链中的 GCC 应与 _BeagleBone Blue_ 内核兼容。
         通常建议选择版本号不超过 _BeagleBone Blue_ OS 镜像自带 GCC 版本的工具链。
         :::

         下载并解压 [gcc-linaro-13.0.0-2022.06-x86_64_arm-linux-gnueabihf.tar.xz](https://snapshots.linaro.org/gnu-toolchain/13.0-2022.06-1/arm-linux-gnueabihf/gcc-linaro-13.0.0-2022.06-x86_64_arm-linux-gnueabihf.tar.xz) 到 bbblue_toolchain 文件夹。

         _BeagleBone Blue_ 的不同 ARM 交叉编译器版本可在 [Linaro 工具链二进制文件网站](http://www.linaro.org/downloads/) 找到。

         ```sh
         wget https://snapshots.linaro.org/gnu-toolchain/13.0-2022.06-1/arm-linux-gnueabihf/gcc-linaro-13.0.0-2022.06-x86_64_arm-linux-gnueabihf.tar.xz
         tar -xf gcc-linaro-13.0.0-2022.06-x86_64_arm-linux-gnueabihf.tar.xz
         ```

         :::tip
         工具链中的 GCC 版本应与 _BeagleBone Blue_ 内核兼容。
         :::

         通常建议选择版本号不超过 _BeagleBone Blue_ OS 镜像自带 GCC 版本的工具链。

      1. 将路径添加到 ~/.profile 中，如下所示：

         ```sh
         export PATH=$PATH:/opt/bbblue_toolchain/gcc-arm-linux-gnueabihf/gcc-linaro-13.0.0-2022.06-x86_64_arm-linux-gnueabihf/bin
         ```

         ::: info
         注销并重新登录以应用更改，或者在当前 shell 中执行相同行。
         :::

      1. 通过下载 PX4 源代码并运行设置脚本来安装其他依赖项：

         ````
         git clone https://github.com/PX4/PX4-Autopilot.git --recursive
         ols
         ```

         您可能需要编辑上传目标以匹配您的设置：

         ```sh
         nano PX4-Autopilot/boards/beaglebone/blue/cmake/upload.cmake

         # 在第 37 行将 debian@beaglebone.lan 改为 root@beaglebone (或 root@<IP>)
         ````

         有关更多信息，请参阅 [开发环境设置](../dev_setup/dev_env_linux_ubuntu.md) 指南。

### 交叉编译和上传

编译和上传

```
make beaglebone_blue_default upload
```

::: info
不使用 upload 时，文件存储在本地构建文件夹中。
:::

要在 _BeagleBone Blue_ 板上测试上传的文件，运行以下命令：

```sh
cd /home/debian/px4
sudo ./bin/px4 -s px4.config
```

::: info
目前 _librobotcontrol_ 需要 root 权限。
:::

<a id="native_builds"></a>

## 本地构建（可选）

您也可以在 BeagleBone Blue 上直接进行 PX4 的本地构建。

在获取预构建库后，

1. 选择 _librobotcontrol_ 安装目录，并通过设置 `LIBROBOTCONTROL_INSTALL_DIR` 环境变量指定路径，以避免包含不需要的头文件
1. 将 **robotcontrol.h** 和 **rc/\*** 安装到 `$LIBROBOTCONTROL_INSTALL_DIR/include`
1. 将预构建的本地（ARM）版本 librobotcontrol.\* 安装到 `$LIBROBOTCONTROL_INSTALL_DIR/lib`

在 BeagleBone Blue 上执行以下命令（例如通过 SSH 连接）：

1. 安装依赖：

   ```sh
   sudo apt-get update
   sudo apt-get install cmake python3-empy=3.3.4-2
   ```
1. 将 PX4 Firmware 直接克隆到 BeagleBone Blue。
1. 继续执行 [标准构建系统安装](../dev_setup/dev_env_linux.md)。

## 配置更改

所有更改均可直接在 beaglebone 上的 px4.config 文件中完成。  
例如，可以将 WIFI 更改为 wlan。

::: info
如果需要永久更改，必须在构建前在构建机上修改 **PX4-Autopilot/posix-configs/bbblue/px4.config** 文件。
:::

## 启动期间的自动启动

以下是示例 [/etc/rc.local]:

```sh
#!/bin/sh -e
#
# rc.local
# 该脚本在每个multiuser runlevel结束时执行。
# 确保脚本在成功时或任何其他情况下都会 "exit 0"
# 错误时的值
#
# 为了启用或禁用此脚本只需修改执行权限
# 位
#
# 默认情况下，此脚本不执行任何操作。

# 等待服务启动
/bin/sleep 25

cd /home/debian/px4

/home/debian/px4/bin/px4 -d -s /home/debian/px4/px4.config > /home/debian/px4/PX4.log &

exit 0
```

下面是 _systemd_ 服务示例 [/lib/systemd/system/px4-quad-copter.service]:

```sh
[Unit]
Description=PX4 Quadcopter Service
After=networking.service network-online.target
StartLimitIntervalSec=0
Conflicts=px4-fixed-wing.service

[Service]
WorkingDirectory=/home/debian/px4
User=root
ExecStart=/home/debian/px4/bin/px4 -d -s /home/debian/px4/px4.config

Restart=on-failure
RestartSec=1

[Install]
WantedBy=multi-user.target
```

### 其他说明

#### 舵机电轨电源

当 PX4 启动时，会自动为舵机供电。

#### 特色功能

BeagleBone Blue 具备一些特色功能，例如多种 WiFi 接口和电源方案的选择。
关于这些功能的使用方法，请参考 **/home/debian/px4/px4.config** 中的注释说明。

#### SBUS 信号转换器

接收机（如 FrSky X8R）输出的 SBUS 信号是反相信号。
BeagleBone Blue 上的 UART 仅支持非反相的 3.3V 电平信号。
[本教程](../tutorials/linux_sbus.md) 包含了 SBUS 信号反相器电路的实现方法。

#### 典型连接方式

对于配备 GPS 和 SBUS 接收机的四旋翼飞行器，典型连接方式如下：

1. 将电机 1、2、3 和 4 的电调分别连接至 BeagleBone Blue 舵机输出的第 1、2、3 和 4 通道。
   如果您的电调接插件包含电源输出引脚，请将其移除，不要连接到 BeagleBone Blue 舵机通道的电源输出引脚。

1. 如果有匹配的 dsm2 接口，请将上述转换后的 SBUS 信号连接至 dsm2 端口，否则请将其连接至任意可用的 UART 接口，并相应修改 **/home/debian/px4/px4.config** 中的对应端口设置。

1. 将 GPS 模块的信号连接至 BeagleBone Blue 上的 GPS 接口。
   请注意 BeagleBone Blue 上 GPS 接口的信号引脚仅支持 3.3V 电压，因此请根据此要求选择合适的 GPS 模块。