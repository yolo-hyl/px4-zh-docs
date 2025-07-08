# 机体（机架）选择

安装固件后，您需要选择[机体类型和机架配置](../airframes/airframe_reference.md)。  
此操作会为所选机架应用合适的初始参数值，例如机体类型、电机数量、电机相对位置等。  
这些参数后续可在[执行器配置与测试](../config/actuators.md)中根据您的机体进行自定义。

::: tip
如果存在与您的机体品牌和型号匹配的机架，请选择它；否则，请选择最接近的"Generic"机架选项。
:::

## 设置机架

设置机架的步骤如下：

1. 启动_QGroundControl_并连接机体。  
1. 选择 **"Q"图标 > 机体设置 > 机架**（侧边栏）以打开_机架设置_界面。  
1. 选择与您的机架最匹配的机体大类/类型，然后在该类别内通过下拉菜单选择最匹配您机体的机架。  

   ![在QGroundControl中选择通用六旋翼X机架](../../assets/qgc/setup/airframe/airframe_px4.jpg)  

   上图示例中选择了_Hexarotor X_组下的_Generic Hexarotor X geometry_。

1. 点击 **Apply and Restart**。  
   在后续提示中点击 **Apply** 以保存设置并重启机体。

   <img src="../../assets/qgc/setup/airframe/airframe_px4_apply_prompt.jpg" width="300px" title="应用机架选择提示" />

## 后续步骤

[执行器配置与测试](../config/actuators.md) 将展示如何设置机体电机和执行器的精确几何参数及其与飞控输出的映射关系。  
在完成执行器到输出的映射后，如果使用PWM或OneShot电调，应进行[电调校准](../advanced_config/esc_calibration.md)。

## 附加信息

- [QGroundControl用户指南 > 机架](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/setup_view/airframe.html)  
- [PX4设置视频 - @37s](https://youtu.be/91VGmdSlbo4?t=35s) (Youtube)