# IrlockReport (UORB消息)

IRLOCK_REPORT消息数据

[source file](https://github.com/PX4/PX4-Autopilot/blob/main/msg/IrlockReport.msg)

```c
# IRLOCK_REPORT消息数据

uint64 timestamp		# 系统启动后经过的时间（微秒）

uint16 signature

# 沿着相机光学轴线观察时，x轴指向右侧，y轴指向下侧，z轴沿着光学轴线。
float32 pos_x # 正切值(theta)，theta是目标与相机中心投影在相机x轴方向的角度
float32 pos_y # 正切值(theta)，theta是目标与相机中心投影在相机y轴方向的角度
float32 size_x #/** 目标在相机x轴方向上的尺寸单位为正切值 **/
float32 size_y #/** 目标在相机y轴方向上的尺寸单位为正切值 **/

```