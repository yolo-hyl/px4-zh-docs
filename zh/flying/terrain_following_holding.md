# 地形跟随/保持

PX4 支持 [地形跟随](#terrain_following) 和 [地形保持](#terrain_hold) 功能，适用于配备了 [距离传感器](../sensor/rangefinders.md) 的多旋翼和 *VTOL机体在MC模式* 下的 [Position](../flight_modes_mc/position.md) 和 [Altitude modes](../flight_modes_mc/altitude.md) 模式。

::: info
PX4 本身并不原生支持任务中的地形跟随功能。
*QGroundControl* 可用于定义任务来 *近似* 跟随地形（这种方式仅根据地图数据库中航点的地形高度设置航点高度）。
:::

<a id="terrain_following"></a>

## 地形跟随

*地形跟随* 功能使机体在低空飞行时能够自动保持相对恒定的离地高度。这对于避开障碍物以及在地形起伏区域保持恒定高度（例如航拍）非常有用。

:::tip
此功能可启用在配备了 [距离传感器](../sensor/rangefinders.md) 的多旋翼和 *VTOL机体在MC模式* 下的 [Position](../flight_modes_mc/position.md) 和 [Altitude modes](../flight_modes_mc/altitude.md) 模式。
:::

当启用 *地形跟随* 时，PX4 使用 EKF 估计器的输出来提供高度估计，并通过另一个估计器从距离传感器测量中计算出的地形高度来提供高度设定值。随着离地距离的变化，高度设定值会调整以保持离地高度恒定。

在较高高度（当估计器报告距离传感器数据无效时），机体将切换到 *高度跟随* 模式，通常使用绝对高度传感器以近似恒定的海拔高度飞行。

::: info
更准确地说，机体将使用 [Using PX4's Navigation Filter (EKF2) > Height](../advanced_config/tuning_the_ecl_ekf.md#height) 中定义的选定高度数据源。
:::

通过设置 [MPC_ALT_MODE](../advanced_config/parameter_reference.md#MPC_ALT_MODE) 为 `1` 可启用地形跟随功能。

<a id="terrain_hold"></a>

## 地形保持

*地形保持* 功能利用距离传感器帮助机体在低空悬停时更好地保持恒定的离地高度，避免因气压计漂移或旋翼下洗气流对气压计的干扰导致的高度变化。

::: info
此功能可启用在配备了 [距离传感器](../sensor/rangefinders.md) 的多旋翼和 *VTOL机体在MC模式* 下的 [Position](../flight_modes_mc/position.md) 和 [Altitude modes](../flight_modes_mc/altitude.md) 模式。
:::

当水平移动（`speed >` [MPC_HOLD_MAX_XY](../advanced_config/parameter_reference.md#MPC_HOLD_MAX_XY)）或高于距离传感器提供有效数据的高度时，机体将切换到 *高度跟随* 模式。

通过设置 [MPC_ALT_MODE](../advanced_config/parameter_reference.md#MPC_ALT_MODE) 为 `2` 可启用地形保持功能。

::: info
*地形保持* 的实现方式与 [地形跟随](#terrain_following) 类似。
它使用 EKF 估计器的输出来提供高度估计，并通过一个单独的单状态地形估计器从距离传感器测量中计算出的地形高度来提供高度设定值。
如果由于外部因素导致离地距离变化，高度设定值会调整以保持离地高度恒定。
:::