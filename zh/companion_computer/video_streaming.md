# 视频流（配套计算机/QGroundControl）

基于PX4的机体支持通过连接到[配套计算机](../companion_computer/index.md)的相机进行视频流传输。

::: info
您无法直接从连接到PX4的相机进行视频流传输。
:::

通过IP链路使用GStreamer将视频发送到_QGroundControl_。要支持流媒体用例，您需要在配套计算机和运行_QGroundControl_的系统上安装_GStreamer_开发包。_QGroundControl_使用GStreamer 1.18.1和简化版的_QtGstreamer_来支持UDP RTP和RSTP视频流。

## 配套计算机设置

您需要安装与QGC使用的_GStreamer_包相匹配的包。

对于Ubuntu配套计算机，最小安装集可能包括：

```sh
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
```

对于完整安装集，可以镜像QGC通过[/tools/setup/install-dependencies-debian.sh](https://github.com/mavlink/qgroundcontrol/blob/master/tools/setup/install-dependencies-debian.sh)安装的依赖项。当前为：

```sh
DEBIAN_FRONTEND=noninteractive apt-get -y --quiet install \
    libgstreamer1.0-dev \
    libgstreamer-plugins-bad1.0-dev \
    libgstreamer-plugins-base1.0-dev \
    libgstreamer-plugins-good1.0-dev \
    libgstreamer-gl1.0-0 \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-plugins-rtp \
    gstreamer1.0-gl \
    gstreamer1.0-libav \
    gstreamer1.0-rtsp \
    gstreamer1.0-vaapi \
    gstreamer1.0-x
```

::: tip
对于其他配套平台，可以参考[对应安装脚本](https://github.com/mavlink/qgroundcontrol/tree/master/tools/setup)中的说明。
:::

配套计算机启动流的通用说明请参阅[QGroundControl VideoReceiver GStreamer README](https://github.com/mavlink/qgroundcontrol/blob/master/src/VideoManager/VideoReceiver/GStreamer/README.md)。

相机和数据链路的设置取决于多种因素。本库中的示例如下（注意，这些是选项，而非“推荐”）：

- [使用WFB-ng Wifi的视频流](../companion_computer/video_streaming_wfb_ng_wifi.md)（教程）：使用RPi和WiFi模块在未连接（广播）模式下传输视频，并作为双向遥测链路。

## QGC设置

要使用QGC进行视频流设置：

1. 如果使用Ubuntu QGC 4发布版构建（或更早版本），请安装GStreamer

   ::: tip
   所有其他QGroundControl构建版本（包括每日构建、4版本之后的发布版，以及其他平台的构建）均包含GStreamer二进制文件。
   :::

   使用以下命令安装运行时依赖项：

   ```sh
   sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
   ```

1. 启动QGC
1. 在_飞行视图_中启用视频：[QGroundControl > 通用设置（设置视图）> 视频](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/settings_view/general.html#video)
1. 如果一切正常，您应在QGC视频切换器（QGC飞行视图左下角）中看到视频流。您可点击视频切换器切换全屏显示，如下截图所示。

   ![QGC显示视频流](../../assets/videostreaming/qgc-screenshot.png)

## Gazebo Classic模拟

[Gazebo Classic](../sim_gazebo_classic/index.md)支持从模拟环境中进行视频流传输。更多信息请参见[Gazebo Classic模拟 > 视频流](../sim_gazebo_classic/index.md#video-streaming)。