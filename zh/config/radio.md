# 无线电控制（RC）设置

_Radio Setup_ 屏幕用于配置您的遥控器的姿态控制杆（roll, pitch, yaw, throttle）到通道的映射，并校准所有其他发射机控制/RC通道的最小值、最大值、中立点和反向设置。

::: info
[Joystick](../config/joystick.md) 可以用于代替RC进行手动控制。
[COM_RC_IN_MODE](../advanced_config/parameter_reference.md#COM_RC_IN_MODE) 参数 [可以设置](../advanced_config/parameters.md) 以定义启用的手动控制器类型。
:::

## 接收器的绑定

在对无线电系统进行校准之前，接收器和发射器必须连接/绑定。  
发射器和接收器对的绑定过程是硬件特定的（请参见您的RC手册获取说明）。

::: info
如果您使用的是 _Spektrum_ 接收器，可以通过 _QGroundControl_ 将其进入绑定模式，如[下方所示](#spectrum-bind)。
:::

::: info
如果您使用的是 _FrSky_ 接收器，可以通过[此处](https://www.youtube.com/watch?v=1IYg5mQdLVI)的说明，使用其发射器进行绑定。
:::

## 遥控信号丢失检测

PX4 需要能够检测遥控器信号丢失情况，以便采取[适当的安全措施](../config/safety.md#manual-control-loss-failsafe)。

遥控接收机有不同的信号丢失指示方式：

- 无输出（PX4会自动检测）
- 输出最低油门值（可配置PX4进行检测）
- 输出最后一次接收到的信号（PX4无法检测，因为看起来是有效输入）

如果您的遥控接收机不支持在信号丢失时输出无信号，请配置其改为输出最低油门值，并在 [RC_FAILS_THR](../advanced_config/parameter_reference.md#RC_FAILS_THR) 中设置对应的值。

操作方法是：将遥控器的油门杆和中立点调至最低位置，将由此产生的PWM输出值同时设置在PX4和接收机中（查阅接收机手册以确定如何设置信号丢失值），然后将油门杆中立点恢复到正常位置。此过程确保信号丢失值低于接收机在正常运行时输出的最小值。

::: info
请不要使用不支持上述两种遥控信号丢失检测方法之一的接收机！
:::

## 执行校准

校准过程非常简单 - 系统会要求你按照屏幕上右上角发射器图示中显示的特定模式移动摇杆。

要校准遥控器：

1. 打开你的遥控发射机。
1. 启动 _QGroundControl_ 并连接机体。
1. 在顶部工具栏中选择 **齿轮** 图标（机体设置），然后在侧边栏中选择 **Radio**。
1. 按 **OK** 开始校准。

   ![无线电设置 - 开始前](../../assets/qgc/setup/radio/radio_start_setup.jpg)

1. 选择与你的发射机匹配的[发射机模式](../getting_started/rc_transmitter_receiver.md#transmitter_modes)单选按钮（这将确保 _QGroundControl_ 在校准期间为你显示正确的摇杆位置）。

   ![无线电设置 - 移动摇杆](../../assets/qgc/setup/radio/radio_sticks_throttle.jpg)

1. 将摇杆移动到文本（和发射机图像）中指示的位置。当摇杆到位后按 **Next**。重复所有位置的操作。
1. 当提示时，将所有其他开关和旋钮移动到其完整范围（你可以在 _通道监视器_ 上观察到它们的移动）。

1. 按 **Next** 保存设置。

无线电校准在[此处的自动驾驶仪设置视频](https://youtu.be/91VGmdSlbo4?t=4m30s)（youtube）中演示。

## 额外的遥控器设置

除了校准遥控器摇杆和其他发射机控制外，此界面还提供了一些额外的遥控器设置选项，可能对您有帮助。

<img src="../../assets/qgc/setup/radio/radio_additional_radio_setup.jpg" title="遥控器设置 - 额外设置" width="300px" />

### Spektrum 绑定

在进行遥控器校准之前，接收机和发射机必须连接/绑定。如果您使用的是 _Spektrum_ 接收机，可以通过 _QGroundControl_ 将其置于 _绑定模式_，操作步骤如下（如果您的机体接收机不易于物理访问，此方法特别有用）。

要绑定 Spektrum 发射机/接收机：

1. 选择 **Spektrum 绑定** 按钮
1. 选择您的接收机对应的单选按钮
1. 点击 **确定**

   ![Spektrum 绑定](../../assets/qgc/setup/radio/radio_additional_setup_spectrum_bind_select_channels.jpg)

1. 在按住绑定按钮的同时打开 Spektrum 发射机电源。

### 中立点复制

此设置用于从遥控器复制手动中立点设置，以便在自动驾驶仪中自动应用。完成后，您需要将手动设置的中立点重置为零。

::: info
中立点设置用于调整滚转、俯仰和偏航，使遥控器摇杆居中时获得稳定的飞行（在稳定飞行模式下）。
某些遥控器提供中立点旋钮，允许为每个摇杆位置发送的值提供偏移。
此处的 **中立点复制** 设置会将偏移量移至自动驾驶仪。
:::

复制中立点的操作步骤：

1. 选择 **中立点复制**。
1. 将摇杆居中并将油门杆完全下压。
1. 点击 **确定**。

   ![中立点复制](../../assets/qgc/setup/radio/radio_additional_radio_setup_copy_trims.jpg)

1. 将遥控器上的中立点重置为零。

### 辅助直通通道

辅助直通通道允许您通过遥控器控制任意可选的硬件（例如，夹爪）。

要使用辅助直通通道：

1. 将最多 2 个遥控器控制映射到独立的通道。
1. 分别指定这些通道映射到 AUX1 和 AUX2 端口，如下所示。
   值在设置后立即保存到机体。

   ![AUX1 和 AUX2 遥控直通通道](../../assets/qgc/setup/radio/radio_additional_setup_aux_passthrough_channels.jpg)

飞控将把指定通道的原始值通过 AUX1/AUX2 传递给连接的舵机/继电器，以驱动您的硬件。

### 参数调节通道

调节通道允许您将遥控器调节旋钮映射到参数（以便您可以动态地从遥控器修改参数）。

:::tip
此功能旨在支持手动飞行中调节：[多旋翼 PID 调整指南](../config_mc/pid_tuning_guide_multicopter.md)，[固定翼 PID 调整指南](../config_fw/pid_tuning_guide_fixedwing.md)。
:::

用于参数调节的通道在 _遥控器_ 设置中分配（此处！），而每个调节通道与对应参数的映射在 _参数编辑器_ 中定义。

设置调节通道的步骤：

1. 将最多 3 个遥控器控制（旋钮或滑块）映射到独立的通道。
1. 使用下拉列表选择 _参数调节 ID_ 到遥控器通道的映射。
   值在设置后立即保存到机体。

   ![映射遥控器通道到调节通道](../../assets/qgc/setup/radio/radio_additional_radio_setup_param_tuning.jpg)

将参数调节通道映射到参数的步骤：

1. 打开 **参数** 侧边栏。
1. 选择要映射到遥控器的参数（这将打开 _参数编辑器_）。
1. 勾选 **高级设置** 复选框。
1. 点击 **设置遥控器到参数...** 按钮（这将弹出以下对话框）

   ![映射调节通道到参数](../../assets/qgc/setup/radio/parameters_radio_channel_mapping.jpg)

1. 从 _参数调节 ID_ 下拉列表中选择要映射的调节通道（1、2 或 3）。
1. 点击 **确定** 关闭对话框。
1. 点击 **保存** 保存所有更改并关闭 _参数编辑器_。

:::tip
您可以通过选择菜单 **工具 > 清除遥控器到参数** 来清除所有参数/调节通道映射，该菜单位于 _参数_ 界面右上角。
:::

## 进一步信息

- [QGroundControl > 无线电控制](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/radio.html)
- [PX4 设置视频 - @4m30s](https://youtu.be/91VGmdSlbo4?t=4m30s) (Youtube)
- [RC系统选择](../getting_started/rc_transmitter_receiver.md) - 选择兼容的RC系统。