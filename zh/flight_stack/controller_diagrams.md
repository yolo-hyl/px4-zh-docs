

# 控制器图示

本节包含主要 PX4 控制器的图示。

这些图示使用标准 [PX4 表示法](../contribute/notation.md)（每个图示均配有带注释的图例）。

<!--    这些图示使用 LaTeX / TikZ 创建。
        代码位于 assets/diagrams/mc_control_arch_tikz.tex。
        生成和编辑这些图示最简单的方法是将代码复制并粘贴到 Overleaf (www.overleaf.com/) 文档中查看输出结果。
-->

## 多旋翼控制架构

![MC Controller Diagram](../../assets/diagrams/mc_control_arch.jpg)

* 这是一个标准的串级控制架构。
* 控制器混合使用P和PID控制器。
* 估计值来自[EKF2](../advanced_config/tuning_the_ecl_ekf.md)。
* 根据模式不同，外环（位置）会被绕过（如外环后的多路复用器所示）。
  位置环仅在保持位置或某轴请求的速度为零时使用。

### 多旋翼角速率控制器

![MC Rate Control Diagram](../../assets/diagrams/mc_angular_rate_diagram.jpg)

* K-PID控制器。详情请参阅[角速率控制器](../config_mc/pid_tuning_guide_multicopter.md#rate-controller)。
* 积分项权限受到限制以防止积分饱和。
* 输出值在控制分配模块中受到限制，通常为-1和1。
* 微分路径使用低通滤波器(LPF)来减少噪声（陀螺驱动器向控制器提供滤波后的微分值）。

  ::: info
  IMU数据处理流程为：
  陀螺数据 > 应用校准参数 > 去除估计偏差 > 陷波滤波器(`IMU_GYRO_NF0_BW`和`IMU_GYRO_NF0_FRQ`) > 低通滤波器(`IMU_GYRO_CUTOFF`) > 机体角速度（被P和I控制器使用的滤波后角速率） > 微分 -> 低通滤波器(`IMU_DGYRO_CUTOFF`) > 机体角加速度（被D控制器使用的滤波后角加速度）

  ![IMU pipeline](../../assets/diagrams/px4_imu_pipeline.png)
  :::

  <!-- 图片源码位于 https://github.com/PX4/PX4-Autopilot/blob/850d0bc588af79186286652af4c8293daafd2c4c/src/lib/mixer/MultirotorMixer/MultirotorMixer.cpp#L323-L326 -->

### 多旋翼姿态控制器

![多旋翼角度控制图](../../assets/diagrams/mc_angle_diagram.jpg)

* 姿态控制器使用了[四元数](https://en.wikipedia.org/wiki/Quaternion)。
* 控制器实现基于这篇[文章](https://www.research-collection.ethz.ch/bitstream/handle/20.500.11850/154099/eth-7387-01.pdf)。
* 调整该控制器时，唯一需要关注的参数是P增益。
* 角速度指令进行了饱和处理。

### 多旋翼加速度到推力和姿态设定值的转换

* 速度控制器生成的加速度设定值将被转换为推力和姿态设定值。
* 转换后的加速度设定值会在垂直和水平推力方向进行饱和处理和优先级分配。
* 推力饱和处理在对应推力计算完成后进行：
   1. 计算所需的垂直推力(`thrust_z`)
   1. 对`thrust_z`进行饱和处理，并使用`MPC_THR_MAX`作为上限
   1. 对`thrust_xy`进行饱和处理，并使用`(MPC_THR_MAX^2 - thrust_z^2)^0.5`作为上限

实现细节可在`PositionControl.cpp`和`ControlMath.cpp`中找到。

### 多旋翼速度控制器

![MC Velocity Control Diagram](../../assets/diagrams/mc_velocity_diagram.png)

* PID控制器用于稳定速度。发出加速度指令。
* 积分器通过限幅方法实现抗积分饱和(ARW)。
* 命令的加速度未被饱和 - 饱和将在转换后的推力设定点结合最大倾斜角时应用。
* 横向增益通过参数 `MPC_XY_VEL_P_ACC`、`MPC_XY_VEL_I_ACC` 和 `MPC_XY_VEL_D_ACC` 设置。
* 垂直增益通过参数 `MPC_Z_VEL_P_ACC`、`MPC_Z_VEL_I_ACC` 和 `MPC_Z_VEL_D_ACC` 设置。

### 多旋翼位置控制器

![MC Position Control Diagram](../../assets/diagrams/mc_position_diagram.png)

* 简单比例控制器，用于发出速度指令。
* 所发出的速度指令通过饱和处理来确保速度在特定范围内。详见参数 `MPC_XY_VEL_MAX`。该参数设置水平方向的最大可能速度。这与最大**期望**速度 `MPC_XY_CRUISE`（自主模式）和 `MPC_VEL_MANUAL`（手动位置控制模式）不同。
* 水平方向比例增益通过参数 `MPC_XY_P` 设置。
* 垂直方向比例增益通过参数 `MPC_Z_P` 设置。

#### 组合位置与速度控制器图

![MC Position Controller Diagram](../../assets/diagrams/px4_mc_position_controller_diagram.png)

* 模式依赖的前馈（ff）——例如，任务模式轨迹生成器（jerk-limited trajectory）计算位置、速度和加速度设定值。
* 加速度设定值（惯性坐标系）将通过偏航设定值转换为姿态设定值（四元数）和总推力设定值。

<!-- The drawing is on draw.io: https://drive.google.com/open?id=13Mzjks1KqBiZZQs15nDN0r0Y9gM_EjtX
请向开发团队申请访问权限。 -->

## 固定翼位置控制器

### Total Energy Control System (TECS)

PX4 对 Total Energy Control System (TECS) 的实现使固定翼飞机能够同时控制真实空速和高度。该代码作为库实现，用于固定翼位置控制模块。

![TECS](../../assets/diagrams/tecs_in_context.svg)

如上图所示，TECS 接收空速和高度设定值作为输入，并输出油门和俯仰角设定值。这两个输出发送至固定翼姿态控制器以实现姿态控制解决方案。然而，如果油门设定值有限且未检测到发动机故障，则直接传递油门设定值。因此需要理解 TECS 的性能直接受俯仰控制环路性能的影响，空速和高度跟踪不佳通常由飞机俯仰角跟踪不佳引起。

::: info
在调整 TECS 之前请确保已调整姿态控制器。
:::

同时控制真实空速和高度并非简单任务。增加飞机俯仰角会导致高度增加但空速降低，增加油门会提升空速但因升力增加也会提升高度。因此我们有两输入（俯仰角和油门）同时影响两输出（空速和高度），这使得控制问题具有挑战性。

TECS 通过将问题转换为能量形式而非原始设定值来提供解决方案。飞机总能量是动能与势能之和，推力（通过油门控制）增加飞机总能量状态。特定总能量状态可通过任意势能与动能组合实现。换句话说，从总能量角度看，高海拔低速飞行等效于低海拔高速飞行。我们称其为特定能量平衡，该值由当前高度和真实空速设定值计算得出。特定能量平衡通过飞机俯仰角进行控制，增加俯仰角将动能转换为势能，负俯仰角则相反。通过将初始设定值转换为可独立控制的能量量，控制问题得以解耦。我们使用推力调节机体的特定总能量，通过俯仰角维持势能（高度）与动能（速度）之间的特定平衡。

#### 总能量控制环

![能量环](../../assets/diagrams/TECS_throttle.png)

<!-- https://drive.google.com/file/d/1q12b6ASbQRkFWqLMXm92cryOI-cZnrKv/view?usp=sharing -->

#### 总能量平衡控制环

![能量平衡环](../../assets/diagrams/TECS_pitch.png)

<!-- 该图表在 draw.io 上: https://drive.google.com/file/d/1bZtFULYmys-_EQNhC9MNcKLFauc7OYJZ/view -->

飞行器的总能量是动能与势能的总和：

$$E_T = \frac{1}{2} m V_T^2 + m g h$$

对其求时间导数可得总能量变化率：

$$\dot{E_T} = m V_T \dot{V_T} + m g \dot{h}$$

由此可推导出比能率：

$$\dot{E} = \frac{\dot{E_T}}{mgV_T}  = \frac{\dot{V_T}}{g} + \frac{\dot{h}}{V_T} = \frac{\dot{V_T}}{g} + sin(\gamma)$$

其中 $\gamma{}$ 为飞行路径角。
对于小角度 $\gamma{}$ 可以近似为：

$$\dot{E} \approx  \frac{\dot{V_T}}{g} + \gamma$$

从飞行器的动力学方程中可得以下关系：

$$T - D = mg(\frac{\dot{V_T}}{g} + sin(\gamma)) \approx mg(\frac{\dot{V_T}}{g} + \gamma)$$

其中 T 和 D 分别为推力与阻力。
在平飞状态下，初始推力与阻力平衡，推力变化则为：

$$\Delta T = mg(\frac{\dot{V_T}}{g} + \gamma)$$

由此可见，$\Delta T{}$ 与 $\dot{E}{}$ 成正比，因此推力设定值应用于总能量控制。

另一方面，升降舵控制是能量保守的，用于势能与动能的相互转换。为此定义了比能平衡率：

$$\dot{B} = \gamma - \frac{\dot{V_T}}{g}$$

## 固定翼姿态控制器

![FW Attitude Controller Diagram](../../assets/diagrams/px4_fw_attitude_controller_diagram.png)

<!-- The drawing is on draw.io: https://drive.google.com/file/d/1ibxekmtc6Ljq60DvNMplgnnU-JOvKYLQ/view?usp=sharing
Request access from dev team. -->

姿态控制器通过级联环路方法工作。外环计算姿态设定值与估计姿态之间的误差，该误差乘以增益（P控制器）生成角速度设定值。内环随后计算角速度误差，并使用PI（比例+积分）控制器生成期望的角加速度。

控制执行机构（副翼、升降舵、方向舵等）的角位置，通过期望角加速度和系统先验知识，通过控制分配（也称为混合）进行计算。此外，由于控制面在高速时更有效，在低速时效果减弱，控制器（针对巡航速度调校）会根据空速测量值（如果使用此类传感器）进行缩放。

::: info
如果没有使用空速传感器，则固定翼姿态控制器的增益调度将被禁用（处于开环状态）；TECS中也无法/无法通过空速反馈进行修正。
:::

前馈增益用于补偿气动阻尼。基本上，飞机体轴力矩的两个主要成分为：控制面（副翼、升降舵、方向舵等）产生的运动，以及与体轴速率成正比的气动阻尼（抵消运动）。为了保持恒定速率，可通过速率环中的前馈来补偿这种阻尼。

### 转向协调

滚转和俯仰控制器具有相同的结构，且纵向与横向动力学被假设为解耦到足以独立工作的程度。  
然而，偏航控制器通过转向协调约束生成偏航率设定值，以最小化飞机滑行时产生的横向加速度。该转向协调算法完全基于协调转弯几何计算。

$$\dot{\Psi}_{sp} = \frac{g}{V_T} \tan{\phi_{sp}} \cos{\theta_{sp}}$$

偏航率控制器还通过提供额外的方向阻尼来抵消[逆偏航效应](https://youtu.be/sNV_SDDxuWk)，并抑制[Dutch roll模式](https://en.wikipedia.org/wiki/Dutch_roll)。

## VTOL飞行控制器

![VTOL姿态控制器图解](../../assets/diagrams/VTOL_controller_diagram.png)

<!-- 该图在draw.io上: https://drive.google.com/file/d/1tVpmFhLosYjAtVI46lfZkxBz_vTNi8VH/view?usp=sharing
向开发团队申请访问权限 -->

本节简要概述垂直起降(VTOL)飞行器的控制结构。  
VTOL飞行控制器同时包含多旋翼控制器和固定翼控制器，这两个控制器在对应VTOL模式下独立运行，或在模式转换期间协同工作。  
上图展示了一个简化的控制图解。请注意VTOL姿态控制器模块，该模块主要实现不同VTOL模式之间必要的切换与混合逻辑，并在模式转换期间执行特定于VTOL类型的控制动作（例如在标准VTOL前飞转换时逐步启动推力电机）。  
进入该模块的输入信号被称为"虚拟"信号，因为根据当前VTOL模式，部分信号会被控制器忽略。

对于标准VTOL和倾转旋翼VTOL，在转换期间固定翼姿态控制器生成角速度设定值，这些设定值随后输入到单独的角速度控制器中，生成多旋翼和固定翼执行器的扭矩指令。  
对于尾座式VTOL，在转换期间使用的是多旋翼姿态控制器。

VTOL姿态模块的输出为多旋翼和固定翼执行器的独立扭矩和力指令（分别为`机体_torque_setpoint`和`机体_thrust_setpoint`的两个实例）。  
这些指令由特定机型的控制分配类进行处理。

有关VTOL模块内转换逻辑调校的更多信息，请参见[VTOL配置](../config_vtol/index.md)。

### 气动速度缩放

本节的目标是通过公式说明为何以及如何通过气动速度对速率PI控制器和前馈(FF)控制器的输出进行缩放，以提高控制性能。首先我们将展示滚转轴上的简化线性维度力矩方程，接着说明气动速度对直接力矩生成的影响，最后说明气动速度在持续滚转过程中的影响。

如上文固定翼姿态控制器所示，速率控制器会为控制分配器（此处称为“mixer”）生成角加速度设定值。为生成这些期望的角加速度，mixer通过可用的气动控制面（例如：标准飞机通常有两块副翼、两块升降舵和一块方向舵）产生力矩。这些控制面产生的力矩受相对气动速度和空气密度（更精确地说是动态压力）的显著影响。若未进行气动速度缩放，一个针对特定巡航速度精细调校的控制器在高速时会使飞行器产生振荡，在低速时则会表现出追踪性能不佳。

读者应注意真实空速(TAS)与指示空速(IAS)的差异，因为在非海平面飞行时，它们的数值差异显著。

动态压力的定义为：

$$\bar{q} = \frac{1}{2} \rho V_T^2$$

其中 $\rho{}$ 表示空气密度，$V_T{}$ 表示真实空速(TAS)。

以本节后续内容以滚转轴为例，维度滚转力矩可表示为：

$$\ell = \frac{1}{2}\rho V_T^2 S b C_\ell = \bar{q} S b C_\ell$$

其中 $\ell{}$ 为滚转力矩，$b{}$ 为翼展，$S{}$ 为参考面积。

无量纲滚转力矩导数 $C_\ell{}$ 可通过副翼效能导数 $C_{\ell_{\delta_a}}{}$、滚转阻尼导数 $C_{\ell_p}{}$ 和侧滑导数 $C_{\ell_\beta}{}$ 建模：

$$C_\ell = C_{\ell_0} + C_{\ell_\beta}\:\beta + C_{\ell_p}\:\frac{b}{2V_T}\:p + C_{\ell_{\delta_a}} \:\delta_a$$

其中 $\beta{}$ 表示侧滑角，$p{}$ 表示机体滚转率，$\delta_a{}$ 表示副翼偏转量。

假设飞行器为对称（$C_{\ell_0} = 0{}$）且协调（$\beta = 0{}$）的，该方程可通过仅使用滚转阻尼和副翼产生的滚转力矩简化：

$$\ell = \frac{1}{2}\rho V_T^2 S b \left [C_{\ell_{\delta_a}} \:\delta_a + C_{\ell_p}\:\frac{b}{2V_T} \: p \right ]$$

该最终方程作为接下来两个子节的基础，用于确定PI控制器和FF控制器所需的气动速度缩放表达式。

#### 静态扭矩(PI)缩放

在零角速度条件下（$p = 0{}$），阻尼项消失，此时可通过以下公式生成恒定瞬时扭矩：

$$\ell = \frac{1}{2}\rho V_T^2 S b \: C_{\ell_{\delta_a}} \:\delta_a = \bar{q} S b \: C_{\ell_{\delta_a}} \:\delta_a$$

解出 $\delta_a{}$ 得到：

$$\delta_a = \frac{2bS}{C_{\ell_{\delta_a}}} \frac{1}{\rho V_T^2} \ell = \frac{bS}{C_{\ell_{\delta_a}}} \frac{1}{\bar{q}} \ell$$

其中第一个分数是常数项，第二个分数取决于空气密度和真实空速的平方。

进一步分析表明，除了使用空气密度和真实空速(TAS)进行缩放，还可以证明指示空速(IAS，$V_I{}$)本身会随空气密度变化。在低空低速条件下，可通过简单密度误差因子将IAS转换为TAS：

$$V_T = V_I \sqrt{\frac{\rho_0}{\rho}}$$

其中 $\rho_o{}$ 表示海平面标准大气压下的空气密度（15°C）。

通过平方、重排并两边同时添加1/2系数，可推导出动压 $\bar{q}{}$ 的表达式：

$$\bar{q} = \frac{1}{2} \rho V_T^2 = \frac{1}{2} V_I^2 \rho_0$$

由此可以直观看出动压与指示空速平方成正比：

$$\bar{q} \propto V_I^2$$

最终，原本包含真实空速和空气密度的缩放因子可仅通过指示空速表示：

$$\delta_a = \frac{2bS}{C_{\ell_{\delta_a}}\rho_0} \frac{1}{V_I^2} \ell$$

#### 速率(前馈)缩放

速率控制器前馈的主要作用是补偿自然速率阻尼。
从基础维度方程重新开始，但这次在恒速滚转过程中，副翼产生的扭矩应恰好补偿阻尼，即

$$- C_{\ell_{\delta_a}} \:\delta_a = C_{\ell_p} \frac{b}{2 V_T} \: p$$

重新整理以提取理想副翼偏转角度可得

$$\delta_a = -\frac{b \: C_{\ell_p}}{2 \: C_{\ell_{\delta_a}}} \frac{1}{V_T} \: p$$

第一个分数给出了理想前馈值，我们可以看出缩放与真空速呈线性关系。
注意负号随后被滚转阻尼导数吸收，后者本身也是负的。

#### 结论

速率PI控制器的输出必须与指示空速（IAS）的平方进行缩放，而速率前馈（FF）的输出必须与真实空速（TAS）进行缩放

$$\delta_{a} = \frac{V_{I_0}^2}{V_I^2} \delta_{a_{PI}} + \frac{V_{T_0}}{V_T} \delta_{a_{FF}}$$

其中 $V_{I_0}{}$ 和 $V_{T_0}{}$ 分别是配平条件下的指示空速和真实空速。

最后，由于执行器输出是归一化的，并且混合器和舵机模块被认为是线性的，我们可以将上述方程改写如下：

$$\dot{\mathbf{\omega}}_{sp}^b = \frac{V_{I_0}^2}{V_I^2} \dot{\mathbf{\omega}}_{sp_{PI}}^b + \frac{V_{T_0}}{V_T} \dot{\mathbf{\omega}}_{sp_{FF}}^b$$

并直接在滚转率、俯仰率和偏航率控制器中实现。

对于控制性能不直接依赖空速的机体（例如旋翼机如[autogyro](../frames_autogyro/index.md)），可以通过[FW_ARSP_SCALE_EN](../advanced_config/parameter_reference.md#FW_ARSP_SCALE_EN)参数禁用空速缩放功能。

#### 调参建议

该空速缩放算法的优势在于无需进行特定调参。不过，空速测量的质量会直接影响其性能。

此外，为了获得最大的稳定飞行包线，应在机体失速速度与最大空速之间的中间空速值调校姿态控制器（例如：飞行速度在15至25m/s之间的飞机应在20m/s时进行调校）。该"tuning"空速应在[FW_AIRSPD_TRIM](../advanced_config/parameter_reference.md#FW_AIRSPD_TRIM)参数中进行设置。