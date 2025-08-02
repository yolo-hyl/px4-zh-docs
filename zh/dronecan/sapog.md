# Sapog 电调固件

[Sapog](https://github.com/PX4/sapog#px4-sapog) 固件是一款先进的开源无感PMSM/BLDC电机控制器固件，专为电动无人驾驶飞行器的动力系统设计。

虽然可以通过传统PWM输入进行控制，但其设计目标是通过CAN总线使用 [DroneCAN](index.md) 运行。

## 购买渠道

多家供应商提供运行Sapog固件的电调硬件：

- [Zubax Orel 20](https://zubax.com/products/orel_20)
- [Holybro Kotleta20](https://holybro.com/products/kotleta20)

<style>
#image_container {
  height: 100%;
  width: 100%;
  display: flex;
}
.image_column {
  width: 33%;
  text-align: center;
}

</style>

<div id="image_container">
  <div class="image_column">
  <img src="../../assets/peripherals/esc_uavcan_zubax_orel20/orel20_top.jpg" alt="Orel20 - Top"/><br><a href="https://zubax.com/products/orel_20">Zubax Orel 20</a>
  </div>
  <div class="image_column">
    <img src="../../assets/peripherals/esc_uavcan_holybro_kotleta20/kotleta20_top.jpg" alt="Holybro Kotleta20 top" /><br><a href="https://holybro.com/products/kotleta20">Holybro Kotleta20</a>
  </div>
</div>

## 硬件设置

电调通过Pixhawk标准4针JST GH线缆连接到CAN总线。  
更多信息请参考 [CAN布线](../can/index.md#wiring) 指南。  
电调顺序无关紧要。

## 固件设置

电调出厂时已预装Sapog固件。若需升级：

编译固件：

```sh
git clone --recursive https://github.com/PX4/sapog
cd sapog/firmware
make RELEASE=1
```

这将生成 `*.application.bin` 文件至 `build/` 目录。  
该二进制文件可通过DroneCAN通过自动驾驶仪上的Sapog引导程序烧录。  
详见 [DroneCAN固件更新](index.md#firmware-update)。

更多详情请参考 [项目页面](https://github.com/PX4/sapog)，包括如何不用DroneCAN引导程序烧录（例如在未编程的设备上）或开发相关内容。

## 飞控设置

### 启用DroneCAN

将电调连接至Pixhawk的CAN总线。使用电池或电源供电（而非仅通过USB为飞控供电），并通过设置参数 [UAVCAN_ENABLE](../advanced_config/parameter_reference.md#UAVCAN_ENABLE) 为 '3' 来启用动态节点ID分配和DroneCAN电调输出。

### 使用QGroundControl自动枚举电调

本节展示如何通过 _QGroundControl_ 自动枚举基于 [Sapog](https://github.com/PX4/sapog#px4-sapog) 的电调。

:::tip
如果配置中只有一个电调，可以跳过本节，因为电调索引默认已设置为零。
:::

枚举电调步骤：

1. 使用电池供电并连接至 _QGroundControl_
2. 在QGC中导航至 **Vehicle Setup > Power**
3. 通过按下 **Start Assignment** 按钮启动电调自动枚举（如截图所示）。

   ![QGC - DroneCAN 电调自动枚举](../../assets/peripherals/esc_qgc/qgc_uavcan_settings.jpg)

   此时您将听到提示音表明飞控已进入电调枚举模式。

4. 按照[机架参考](../airframes/airframe_reference.md)中规定的旋转方向，从第一个电机到最后一个电机依次手动旋转每个电机。  
   每次旋转电机时，您将听到确认蜂鸣声。

   ::: info
   请确保按正确方向旋转每个电机，电调会自动学习并记住方向（即正常运行时顺时针旋转的电机在枚举时也必须顺时针旋转）。
   :::

5. 最后一个电机枚举完成后，提示音将改变以表明枚举程序结束。
6. 重启PX4和Sapog电调以应用新枚举ID。

下视频演示该过程：

<lite-youtube videoid="4nSa8tvpbgQ" title="Zubax Orel 20 with PX4 Flight Stack - Auto-enumeration"/>

### 使用Sapog手动枚举电调

:::tip
我们推荐使用上述的 [QGroundControl自动枚举Sapog电调](#使用QGroundControl自动枚举电调) 方法，而非手动枚举（更简单且安全）。
:::

可使用 [DroneCAN GUI工具](https://dronecan.github.io/GUI_Tool/Overview/) 手动配置电调索引和方向。  
这将为每个枚举的电调分配以下Sapog配置参数：

- `esc_index`
- `ctl_dir`

::: info
参见 [Sapog参考手册](https://files.zubax.com/products/io.px4.sapog/Sapog_v2_Reference_Manual.pdf) 获取参数详情。
:::

### PX4配置

使用 [执行器配置](../config/actuators.md#actuator-testing) 界面将电机分配到输出。

## 故障排查

参见 [DroneCAN故障排查](index.md#troubleshooting)

## 更多信息

- [PX4/Sapog](https://github.com/PX4/sapog#px4-sapog) (Github)
- [Sapog v2参考手册](https://files.zubax.com/products/io.px4.sapog/Sapog_v2_Reference_Manual.pdf)
- [使用基于Sapog的电调与PX4](https://kb.zubax.com/display/MAINKB/Using+Sapog-based+ESC+with+PX4) (Zubax知识库)