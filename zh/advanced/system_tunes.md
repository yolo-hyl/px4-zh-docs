# 系统通知音调

PX4 定义了若干 [标准音调/曲调](../getting_started/tunes.md)，用于提供重要系统状态和问题的音频通知（例如系统启动、解锁成功、电池警告等）。

音调通过字符串（使用 [ANSI音乐符号](http://artscene.textfiles.com/ansimusic/information/ansimtech.txt)）定义，并由代码通过 [tunes](https://github.com/PX4/PX4-Autopilot/tree/main/src/lib/tunes) 库播放。该库还包含默认系统音调列表 - 参见 [lib/tunes/tune_definition.desc](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc)。

PX4 还包含一个模块，可用于播放（测试）默认音调或用户自定义音调。

本主题提供创建自定义音调并添加/替换系统通知音调的通用指导。

## 创建音调

音调字符串使用 [ANSI音乐符号](http://artscene.textfiles.com/ansimusic/information/ansimtech.txt) 定义。

:::tip
更多格式信息可参考 [QBasic PLAY statement](https://en.wikibooks.org/wiki/QBasic/Appendix#PLAY)（Wikibooks）以及 [tune_definition.desc](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc) 中的说明。
:::

创建新音调最简单的方法是使用音乐编辑器。这允许您在计算机上编辑音乐并回放，然后导出为 PX4 可播放的格式。

ANSI 音乐在 ANSI BBS 系统时代很流行，因此最佳编辑工具是 DOS 工具。在 Windows 上，一个选项是使用 _Dosbox_ 中的 _Melody Master_。

软件使用步骤如下：

1. 下载 [DosBox](http://www.dosbox.com/) 并安装应用
1. 下载 [Melody Master](ftp://archives.thebbs.org/ansi_utilities/melody21.zip) 并解压到新目录
1. 打开 _Dosbox_ 控制台
1. 按如下方式在 Dosbox 中挂载 Melody Master 目录：

   ```sh
   mount c C:\<path_to_directory\Melody21
   ```

1. 通过以下命令启动 _Melody Master_：

   ```sh
   c:
   start
   ```

1. 您将看到几个屏幕选项，然后按 **1** 显示 _Melody Master_：
   ![Melody Master 2.1](../../assets/tunes/tunes_melody_master_2_1.jpg)

   屏幕下半部分提供了该工具的键盘快捷键帮助（箭头用于在五线谱中移动，数字用于选择音符长度等）。

1. 当准备好保存音乐时：
   - 按 **F2** 为音调命名并保存到 Melody Master 安装目录的 _/Music_ 子文件夹
   - 按 **F7**，然后在右侧输出格式列表中滚动到 ANSI。文件将被导出到 Melody Master 目录的 _根目录_（同名文件，带特定扩展名）。
1. 打开文件。输出可能如下所示：

   ![ANSI Output from file](../../assets/tunes/tune_musicmaker_ansi_output.png)

1. PX4 可播放的字符串是 `MNT` 和 `P64` 之间的部分：`150L1O3DL16CL32<B>C<AEL16A`

## 测试音调

当准备好在 PX4 上测试新音调时，使用 [tune_control](../modules/modules_system.md#tune-control) 库。例如，要测试上面"创建"的音调，您可以在控制台或 shell（例如 [MAVLink Shell](../debug/mavlink_shell.md)）中输入以下命令：

```sh
tune_control play -m "150L1O3DL16CL32<B>C<AEL16A"
```

::: info
开箱即用时，`tune_control` 仅存在于真实硬件上（而非模拟器）。
:::

## 替换现有音调

音调在 [tune_definition.desc](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/tunes/tune_definition.desc) 中定义。

如果只需要替换现有音调，则可以在自己的 fork 中替换文件，并更新 `PX4_DEFINE_TUNE` 中定义的音调字符串。

## 添加新音调

TBD。

<!--

1. 假设需要在文件中定义新的 `PX4_DEFINE_TUNE` 并分配唯一编号。
2. 需要查看音调播放方式。留待日后解决。

-->