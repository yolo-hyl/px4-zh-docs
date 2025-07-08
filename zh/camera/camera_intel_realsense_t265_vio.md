# Intel® RealSense™ Tracking Camera T265（VIO）

[Intel® RealSense™ Tracking Camera T265](https://www.intelrealsense.com/tracking-camera-t265/) 提供可被用于 [VIO](../computer_vision/visual_inertial_odometry.md) 的里程计信息，可增强或替代 PX4 上的其他定位系统。

:::tip
该摄像头是建议使用的设备，并被用于 [Visual Inertial Odometry (VIO) > Suggested Setup](../computer_vision/visual_inertial_odometry.md#suggested-setup)。
:::

![Intel® RealSense™ Tracking Camera T265 - Angled Image](../../assets/peripherals/camera_vio/t265_intel_realsense_tracking_camera_photo_angle.jpg)

## 购买渠道

[Intel® RealSense™ Tracking Camera T265](https://www.intelrealsense.com/tracking-camera-t265/) (store.intelrealsense.com)

## 设置说明

总体步骤如下：

- 应使用 Intel 提供的 [`realsense-ros` wrapper](https://github.com/IntelRealSense/realsense-ros) 从摄像头中提取原始数据。
- 摄像头应以镜头朝下（默认）方式安装。  
  请务必在 ROS 启动文件中通过发布静态变换来指定摄像头方向，例如：
  ```xml
  <node pkg="tf" type="static_transform_publisher" name="tf_baseLink_cameraPose"
      args="0 0 0 0 1.5708 0 base_link camera_pose_frame 1000"/>
  ```
  这是一个静态变换，将摄像头 ROS 帧 `camera_pose_frame` 连接到 MAVROS 无人机帧 `base_link`。
  - 前三个 `args` 指定从飞控中心到摄像头的 _平移_ x,y,z（单位：米）。  
    例如，如果摄像头位于控制器前方 10cm 且上方 4cm，前三个数字应为：[0.1, 0, 0.04,...]
  - 后三个 `args` 指定旋转弧度（偏航、俯仰、翻滚）。  
    因此 `[... 0, 1.5708, 0]` 表示向下俯冲 90°（朝向地面）。正前方则为 [... 0 0 0]。
- 摄像头对高频振动敏感！  
  应使用减震材料（例如减震泡沫）进行软安装。