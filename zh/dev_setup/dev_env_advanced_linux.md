# 高级 Linux 安装使用场景

## 使用 JTAG 编程适配器

Linux 用户需要明确允许 JTAG 编程适配器访问 USB 总线。

::: info
对于 Archlinux：请将以下命令中的用户组 plugdev 替换为 uucp
:::

以 `sudo` 权限运行简单的 `ls` 命令，确保后续命令能成功执行：

```sh
sudo ls
```

然后临时获取 `sudo` 权限后运行以下命令：

```sh
cat > $HOME/rule.tmp <<_EOF
# 所有 3D Robotics（包括 PX4）设备
SUBSYSTEM=="usb", ATTR{idVendor}=="26AC", GROUP="plugdev"
# FTDI（以及 Black Magic Probe）设备
SUBSYSTEM=="usb", ATTR{idVendor}=="0483", GROUP="plugdev"
# Olimex 设备
SUBSYSTEM=="usb",  ATTR{idVendor}=="15ba", GROUP="plugdev"
_EOF
sudo mv $HOME/rule.tmp /etc/udev/rules.d/10-px4.rules
sudo /etc/init.d/udev restart
```

需要将用户添加到 **plugdev** 用户组：

```sh
sudo usermod -a -G plugdev $USER
```