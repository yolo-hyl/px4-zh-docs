# CentOS 开发环境

:::warning
此开发环境由[社区支持和维护](../advanced/community_supported_dev_env.md)。  
可能与当前版本的PX4兼容或不兼容。  

有关核心开发团队支持的环境和工具信息，请参见[Toolchain Installation](../dev_setup/dev_env.md)。
:::

构建需要Python 2.7.5。因此截至目前，建议使用Centos 7。  
(对于早期Centos版本可并行安装python v2.7.5，但不建议这样操作，因为可能导致yum损坏。)

## 常见依赖项

EPEL仓库需要用于openocd libftdi-devel libftdi-python

```sh
wget https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
sudo yum install epel-release-7-5.noarch.rpm
yum update
yum groupinstall “Development Tools”
yum install python-setuptools python-numpy
easy_install pyserial
easy_install pexpect
easy_install toml
easy_install pyyaml
easy_install cerberus
yum install openocd libftdi-devel libftdi-python python-argparse flex bison-devel ncurses-devel ncurses-libs autoconf texinfo libtool zlib-devel cmake vim-common
```

::: info
您可能还想要安装 `python-pip` 和 `screen`。
:::

## GCC 工具链安装

<!-- GCC toolchain documentation used for all Linux platforms to build NuttX -->

执行以下脚本安装GCC 7-2017-q4:

:::warning
此版本的GCC已过时。  
截至目前Ubuntu的当前版本是 `9-2020-q2-update` (参见[focal nuttx docker file](https://github.com/PX4/PX4-containers/blob/master/docker/Dockerfile_nuttx-focal#L28))
:::

```sh
pushd .
cd ~
wget https://armkeil.blob.core.windows.net/developer/Files/downloads/gnu-rm/7-2017q4/gcc-arm-none-eabi-7-2017-q4-major-linux.tar.bz2
tar -jxf gcc-arm-none-eabi-7-2017-q4-major-linux.tar.bz2
exportline="export PATH=$HOME/gcc-arm-none-eabi-7-2017-q4-major/bin:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
popd
```

现在重启您的机器。

**故障排除**

通过输入以下命令检查版本：

```sh
arm-none-eabi-gcc --version
```

输出应类似：

```sh
arm-none-eabi-gcc (GNU Tools for Arm Embedded Processors 7-2017-q4-major) 7.2.1 20170904 (release) [ARM/embedded-7-branch revision 255204]
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

<!-- import docs ninja build system -->

## Ninja 构建系统

[Ninja](https://ninja-build.org/) 比 _Make_ 更快，PX4 _CMake_ 生成器支持它。

在Ubuntu Linux上，您可以从常规仓库自动安装：

```sh
sudo apt-get install ninja-build -y
```

其他系统可能未在包管理器中包含Ninja。  
此时可下载二进制文件并添加到路径：

```sh
mkdir -p $HOME/ninja
cd $HOME/ninja
wget https://github.com/martine/ninja/releases/download/v1.6.0/ninja-linux.zip
unzip ninja-linux.zip
rm ninja-linux.zip
exportline="export PATH=$HOME/ninja:\$PATH"
if grep -Fxq "$exportline" ~/.profile; then echo nothing to do ; else echo $exportline >> ~/.profile; fi
. ~/.profile
```