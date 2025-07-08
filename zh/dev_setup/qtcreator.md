# Qt Creator IDE

:::warning
此开发环境由[社区支持和维护](../advanced/community_supported_dev_env.md)。
它可能与当前版本的PX4兼容，也可能不兼容。

Qt Creator 已被 [VSCode](../dev_setup/vscode.md) 取代，作为PX4开发的官方支持（推荐）IDE。
有关核心开发团队支持的环境和工具的信息，请参见[工具链安装](../dev_setup/dev_env.md)。
:::

[Qt Creator](https://www.qt.io/download-open-source) 是一个流行的跨平台开源IDE，可用于编译和调试PX4。

## Qt Creator 功能

Qt creator 提供可点击的符号、完整的代码库自动补全以及构建和烧录固件功能。

![Qt Creator 截图](../../assets/toolchain/qtcreator.png)

下图视频展示了其使用方式。

<lite-youtube videoid="Bkk8zttWxEI" title="(Qt Creator) PX4 Flight Stack Build Experience"/>

## IDE 配置

### Linux 上的 Qt Creator

在启动 Qt Creator 之前，需要创建[项目文件](https://gitlab.kitware.com/cmake/community/-/wikis/doc/cmake/Generator-Specific-Information#codeblocks-generator)：

```sh
cd ~/src/PX4-Autopilot
mkdir ../Firmware-build
cd ../Firmware-build
cmake ../PX4-Autopilot -G "CodeBlocks - Unix Makefiles"
```

然后通过 **File > Open File or Project** 加载 PX4-Autopilot 根目录中的 CMakeLists.txt（选择 CMakeLists.txt 文件）。

加载后，可以通过选择运行目标配置中的 'custom executable'，将 **play** 按钮配置为运行项目，并将可执行文件设置为 'make'，参数设置为 'upload'。

### Windows 上的 Qt Creator

::: info
Windows 上尚未经过 PX4 开发与 Qt Creator 兼容性测试。
:::

### Mac OS 上的 Qt Creator

在启动 Qt Creator 之前，需要创建[项目文件](https://gitlab.kitware.com/cmake/community/-/wikis/doc/cmake/Generator-Specific-Information#codeblocks-generator)：

```sh
cd ~/src/PX4-Autopilot
mkdir -p build/creator
cd build/creator
cmake ../.. -G "CodeBlocks - Unix Makefiles"
```

完成！启动 _Qt Creator_ 并设置项目进行构建。

<!-- note, video here was removed/made private, and in any case out of date. Just hoping people can work it out -->