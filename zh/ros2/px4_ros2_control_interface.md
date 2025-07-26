

# PX4 ROS 2 控制接口

<Badge type="tip" text="PX4 v1.15" /> <Badge type="warning" text="Experimental" />

:::warning Experimental
撰写本文时，PX4 ROS 2 控制接口的部分内容仍处于实验阶段，因此可能会发生变化：

- 用于在 ROS 2 模式中定义模式的架构和核心接口基本稳定，已在持续集成中测试。该库在当前状态下相比使用外部模式提供了显著的优势。
- 仅有少数设定点类型已稳定（其他仍在开发中）。您可能需要使用 PX4 内部主题，这些主题可能不会长期保持向后兼容。
- API 尚未完全文档化。

:::

[PX4 ROS 2 接口库](../ros2/px4_ros2_interface_lib.md)是一个 C++ 库，用于简化从 ROS 2 控制 PX4 的流程。

开发人员使用该库创建并动态注册使用 ROS 2 编写的模式。这些模式会动态注册到 PX4，并在地面站或其他外部系统中显示为 PX4 的一部分。如果 ROS2 模式失败，它们甚至可以替换 PX4 的默认模式，回退到原始版本。

该库还提供了用于发送不同类型设定点的类，从高级导航任务一直到直接执行器控制。这些类抽象了 PX4 内部使用的设定点，因此可用于为未来的 PX4 和 ROS 发行版提供一致的 ROS 2 接口。

相比 PX4 内部模式，PX4 ROS 2 模式更容易实现和维护，并且为开发者提供了更多的处理能力和现有库资源。除非该模式是安全关键型、需要严格时序或非常高的更新率，或者你的机体没有搭载计算模块，否则你应[优先考虑使用 PX4 ROS 2 模式而非 PX4 内部模式](../concept/flight_modes.md#internal-vs-external-modes)。

## 概述

该图提供了控制接口模式和模式执行器与PX4交互的概念性总览。

![ROS2模式概述图](../../assets/middleware/ros2/px4_ros2_interface_lib/ros2_modes_overview.svg)

<!-- Source: https://docs.google.com/drawings/d/1WByCfgcytnaow7r41VhYJL8OGrw1RjFO51GoPMQBCNA/edit -->

以下部分对图中使用的术语进行定义和解释。

### 定义

#### 模式

通过接口库定义的模式具有以下特性：

- 模式是一个组件，可以通过向机体发送设定值来控制其运动（如速度或直接执行器指令）。
- 模式会选择一种设定值类型并在激活时发送该类型值，且可以在多种设定值类型之间切换。
- 模式不能激活其他模式，必须通过用户（通过RC/GCS）、飞行控制器在安全保护情况下、_mode executor_ 或其他外部系统激活。
- 在地面站（GCS）中显示名称。
- 可以配置其模式需求（例如需要有效的位姿估计）。
- 模式可以执行不同任务，例如飞向目标、释放绞盘、投放载荷然后飞回。
- 模式可以替换PX4中定义的模式。

#### 模式执行器

模式执行器是用于调度飞行模式的可选组件。例如，自定义载荷投送或测绘模式的模式执行器可能首先触发起飞，然后切换到自定义模式，当任务完成后触发RTL。

具体而言，它具有以下特性：

- 模式执行器是比飞行模式高一个层级的可选组件。它是一个状态机，可以激活模式并等待其完成。
- 只有在它拥有控制权时才能执行此操作。为此，一个执行器恰好拥有一个_归属模式_（且一个模式最多只能被一个执行器拥有）。该模式作为执行器的激活方式：当用户选择该模式时，拥有该模式的执行器将被激活并可以自主选择任何模式。它会一直保持控制权，直到用户通过遥控器或地面站（GCS）切换模式，或故障安全机制触发模式切换。如果故障安全解除，执行器将重新激活。
- 这允许多个执行器共存。
- 执行器不能激活其他执行器。
- 在库中，模式执行器始终与自定义模式组合实现。

::: info

- 这些定义确保用户可以通过遥控器或地面站随时命令模式切换，从而在任何时刻收回对自定义模式或执行器的控制权。
- 模式执行器对用户是透明的。它通过归属模式间接被选择和激活，因此该模式应相应命名。

:::

#### 配置覆盖

模式和执行器均可定义配置覆盖，允许在模式或执行器激活期间自定义特定行为。

当前已实现的配置包括：

- _禁用自动解除武装_。  
  允许着陆后再次起飞（例如释放载荷时）。

- _能够延迟非关键性安全保护_。  
  允许执行器执行操作时不受非关键性安全保护的中断。  
  例如忽略低电量安全保护以完成绞盘操作。

### 与外部控制的比较

上述概念相比传统的[外部控制](../ros/offboard_control.md)具有以下优势：

- 多个节点或应用程序可以共存甚至同时运行  
  但同一时间只能有一个节点控制机体，且该节点定义明确
- 模式具有唯一名称，并可在地面站显示/选择
- 模式与安全保护状态机和解锁检查集成
- 可发送的设定值类型有明确定义
- ROS 2 模式可替代飞控内部模式（如[Return模式](../flight_modes/return.md)）

## 安装和首次测试

要开始使用需要执行以下步骤：

1. 确保您已准备好[ROS 2 设置](../ros2/user_guide.md)，并在ROS 2工作区中包含 [`px4_msgs`](https://github.com/PX4/px4_msgs)。

2. 将仓库克隆到工作区：

   ```sh
   cd $ros_workspace/src
   git clone --recursive https://github.com/Auterion/px4-ros2-interface-lib
   ```

   ::: info
   为了确保兼容性，请使用PX4、_px4_msgs_ 和库的最新 _main_ 分支。
   详见[此处](https://github.com/Auterion/px4-ros2-interface-lib#compatibility-with-px4)。
   :::

3. 构建工作区：

   ```sh
   cd ..
   colcon build
   source install/setup.bash
   ```

4. 在另一个终端启动PX4 SITL：

   ```sh
   cd $px4-autopilot
   make px4_sitl gazebo-classic
   ```

   （此处使用Gazebo-Classic，您也可以使用任何模型或模拟器）

5. 在新终端运行micro XRCE agent（可保持运行）：

   ```sh
   MicroXRCEAgent udp4 -p 8888
   ```

6. 启动QGroundControl。

   ::: info
   请使用QGroundControl Daily版本，它支持动态更新模式列表。
   :::

7. 返回ROS 2终端，运行其中一个示例模式：

   ```sh
   ros2 run example_mode_manual_cpp example_mode_manual
   ```

   您应看到如下输出显示"My Manual Mode"模式已注册：

   ```sh
   [DEBUG] [example_mode_manual]: Checking message compatibility...
   [DEBUG] [example_mode_manual]: Subscriber found, continuing
   [DEBUG] [example_mode_manual]: Publisher found, continuing
   [DEBUG] [example_mode_manual]: Registering 'My Manual Mode' (arming check: 1, mode: 1, mode executor: 0)
   [DEBUG] [example_mode_manual]: Subscriber found, continuing
   [DEBUG] [example_mode_manual]: Publisher found, continuing
   [DEBUG] [example_mode_manual]: Got RegisterExtComponentReply
   [DEBUG] [example_mode_manual]: Arming check request (id=1, only printed once)
   ```

8. 在PX4终端可检查模式是否注册成功：

   ```sh
   commander status
   ```

   输出应包含：

   ```plain
   INFO  [commander] Disarmed
   INFO  [commander] navigation mode: Position
   INFO  [commander] user intended navigation mode: Position
   INFO  [commander] in failsafe: no
   INFO  [commander] External Mode 1: nav_state: 23, name: My Manual Mode
   ```

9. 此时您应在QGroundControl中看到该模式：

   ![QGC Modes](../../assets/middleware/ros2/px4_ros2_interface_lib/qgc_modes.png)

10. 选择该模式，确保已配置手动控制源（物理或虚拟摇杆），然后武装机体。
    模式将激活，并输出以下内容：

    ```sh
    [DEBUG] [example_mode_manual]: Mode 'My Manual Mode' activated
    ```

11. 现在您可以开始创建自己的模式。

## 如何使用该库

以下章节描述了该库提供的特定功能。  
此外，任何其他PX4主题都可以被订阅或发布。

### 模式类定义

本节将通过示例演示如何为自定义模式创建类。

查看完整的应用程序示例，请参考[Auterion/px4-ros2-interface-lib](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/examples/cpp)仓库中的[示例](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/examples/cpp/modes/manual/include/mode.hpp)，例如[examples/cpp/modes/manual](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/examples/cpp/modes/manual/include/mode.hpp)。

```cpp{1,5,7-9,24-31}
class MyMode : public px4_ros2::ModeBase // [1]
{
public:
  explicit MyMode(rclcpp::Node & node)
  : ModeBase(node, Settings{"My Mode"}) // [2]
  {
    // [3]
    _manual_control_input = std::make_shared<px4_ros2::ManualControlInput>(*this);
    _rates_setpoint = std::make_shared<px4_ros2::RatesSetpointType>(*this);
  }

  void onActivate() override
  {
    // 当模式被选中时调用
  }

  void onDeactivate() override
  {
    // 当模式被停用时调用
  }

  void updateSetpoint(const rclcpp::Duration & dt) override
  {
    // [4]
    const Eigen::Vector3f thrust_sp{0.F, 0.F, -_manual_control_input->throttle()};
    const Eigen::Vector3f rates_sp{
      _manual_control_input->roll() * 150.F * M_PI / 180.F,
      -_manual_control_input->pitch() * 150.F * M_PI / 180.F,
      _manual_control_input->yaw() * 100.F * M_PI / 180.F
    };
    _rates_setpoint->update(rates_sp, thrust_sp);
  }

private:
  std::shared_ptr<px4_ros2::ManualControlInput> _manual_control_input;
  std::shared_ptr<px4_ros2::RatesSetpointType> _rates_setpoint;
};
```

- `[1]`: 首先创建一个继承自[px4_ros2::ModeBase](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1ModeBase.html)的类。
- `[2]`: 在构造函数中传递模式名称。这也可以用于配置其他功能，例如替换飞控器内部模式。
- `[3]`: 这里创建所有后续要用到的对象。可以是遥控输入、设定点类型或遥测数据。`*this`作为`Context`传递给每个对象，将对象与模式关联。
- `[4]`: 当模式处于激活状态时，该方法会定期被调用（更新频率取决于设定点类型）。此处可以执行工作并生成新的设定点。

创建该模式的实例后，必须调用`mode->doRegister()`进行实际注册，该方法与飞控器交互，若注册失败则返回`false`。
如果使用了模式执行器，`doRegister()`必须在模式执行器上调用，而不是在模式上直接调用。

### 模式执行器类定义

本节逐步演示如何创建一个模式执行器类。

```cpp{1,4-5,9-16,20,33-57}
class MyModeExecutor : public px4_ros2::ModeExecutorBase // [1]
{
public:
  MyModeExecutor(rclcpp::Node & node, px4_ros2::ModeBase & owned_mode) // [2]
  : ModeExecutorBase(node, px4_ros2::ModeExecutorBase::Settings{}, owned_mode),
    _node(node)
  { }

  enum class State // [3]
  {
    Reset,
    TakingOff,
    MyMode,
    RTL,
    WaitUntilDisarmed,
  };

  void onActivate() override
  {
    runState(State::TakingOff, px4_ros2::Result::Success); // [4]
  }

  void onDeactivate(DeactivateReason reason) override { }

  void runState(State state, px4_ros2::Result previous_result)
  {
    if (previous_result != px4_ros2::Result::Success) {
      RCLCPP_ERROR(_node.get_logger(), "State %i: previous state failed: %s", (int)state,
        resultToString(previous_result));
      return;
    }

    switch (state) { // [5]
      case State::Reset:
        break;

      case State::TakingOff:
        takeoff([this](px4_ros2::Result result) {runState(State::MyMode, result);});
        break;

      case State::MyMode: // [6]
        scheduleMode(
          ownedMode().id(), [this](px4_ros2::Result result) {
            runState(State::RTL, result);
          });
        break;

      case State::RTL:
        rtl([this](px4_ros2::Result result) {runState(State::WaitUntilDisarmed, result);});
        break;

      case State::WaitUntilDisarmed:
        waitUntilDisarmed([this](px4_ros2::Result result) {
            RCLCPP_INFO(_node.get_logger(), "All states complete (%s)", resultToString(result));
          });
        break;
    }
  }

private:
  rclcpp::Node & _node;
};
```

- `[1]`: 首先我们创建一个继承自 [`px4_ros2::ModeExecutorBase`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1ModeExecutorBase.html) 的类。
- `[2]`: 构造函数接收与执行器关联的自定义模式，并将其传递给 `ModeExecutorBase` 的构造函数。
- `[3]`: 我们定义一个枚举类来表示需要依次执行的状态。
- `[4]`: 当执行器变为活动状态时调用 `onActivate`，此时可以开始执行各个状态。具体实现方式由用户决定，本例中使用 `runState` 方法执行下一个状态。
- `[5]`: 切换到某个状态时，调用 `ModeExecutorBase` 的异步方法启动目标模式：`run`、`takeoff`、`rtl` 等。这些方法会传入一个完成时调用的函数，回调提供 `Result` 参数表示操作是否成功。操作成功时回调会运行下一个状态。
- `[6]`: 我们使用 `scheduleMode()` 方法启动执行器的"所属模式"，遵循与其他状态处理程序相同的模式。

### 设置点类型

一种模式可以选择其希望使用的设置点类型以控制机体。所使用的类型还定义了与不同机体类型的兼容性。

以下部分列出了支持的设置点类型：

- [前往设置点类型](#go-to-setpoint-gotosetpointtype): 平滑的位置和（可选）航向控制  
- [固定翼横向和纵向设置点类型](#fixed-wing-lateral-and-longitudinal-setpoint-fwlaterallongitudinalsetpointtype): <Badge type="tip" text="main (planned for: PX4 v1.17)" /> 直接控制固定翼的横向和纵向动力学  
- [直接执行器控制设置点类型](#direct-actuator-control-setpoint-directactuatorssetpointtype): 直接控制电机和飞行面舵机的设置点  

:::tip  
其他设置点类型目前处于实验阶段，可在以下路径找到：[px4_ros2/control/setpoint_types/experimental](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/px4_ros2_cpp/include/px4_ros2/control/setpoint_types/experimental)。

您可以通过添加继承自 `px4_ros2::SetpointBase` 的类、根据设置点需求设置配置标志，并发布包含设置点的任意主题来添加自定义设置点类型。  
:::

#### 前往设定点 (GotoSetpointType)

::: info
该设定点类型目前仅支持多旋翼。
:::

通过 [`px4_ros2::GotoSetpointType`](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/px4_ros2_cpp/include/px4_ros2/control/setpoint_types/goto.hpp) 设定点类型，可平滑控制位置设定点及（可选）航向角设定点。该设定点类型会基于时间最优、最大加加速度轨迹，通过FMU位置和航向角平滑器进行流式传输，同时考虑速度和加速度约束。

最简单的用法是将3D位置输入update方法：

```cpp
const Eigen::Vector3f target_position_m{-10.F, 0.F, 3.F};
_goto_setpoint->update(target_position_m);
```

在这种情况下，航向角将保持 _未受控_。
若需额外控制航向角，可将其作为第二个输入参数指定：

```cpp
const Eigen::Vector3f target_position_m{-10.F, 0.F, 3.F};
const float heading_rad = 3.14F;
_goto_setpoint->update(
  target_position_m,
  heading_rad);
```

前往设定点的附加功能是动态控制底层平滑器的速度限制（即最大水平/垂直位移速度及航向角速率）。若如上未指定，平滑器将默认使用机体的默认最大值（通常设定为物理限制）。平滑器将 _仅_ 降低速度限制，不会提高。

```cpp
_goto_setpoint->update(
  target_position_m,
  heading_rad,
  max_horizontal_velocity_m_s,
  max_vertical_velocity_m_s,
  max_heading_rate_rad_s);
```

update方法中除位置外的所有参数均以 `std::optional<float>` 模板化，意味着若仅需约束航向角速率而不需要约束位移速度，可通过 `std::nullopt` 实现：

```cpp
_goto_setpoint->update(
  target_position_m,
  heading_rad,
  std::nullopt,
  std::nullopt,
  max_heading_rate_rad_s);
```

#### 固定翼横向和纵向设定点（FwLateralLongitudinalSetpointType）

<Badge type="warning" text="固定翼专用" /> <Badge type="tip" text="main（计划用于：PX4 v1.17）" />

::: info
此设定点类型适用于固定翼机体以及固定翼模式下的垂直起降飞行器（VTOL）。
:::

使用 [`px4_ros2::FwLateralLongitudinalSetpointType`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1FwLateralLongitudinalSetpoint.html) 可直接控制固定翼机体的横向和纵向动力学——即侧向运动（转弯/倾斜）和前进/垂直运动（加速及爬升/下降）。
此设定点通过 PX4 [_FwLateralLongitudinalControl_ 模块](../modules/modules_controller.md#fw-lat-lon-control) 进行处理，该模块可解耦横向和纵向输入，同时确保机体限制条件得到满足。

要控制机体，必须提供至少一个横向 **和** 一个纵向设定点：

1. 对于纵向输入：`altitude` 或 `height_rate` 至少有一个为有效值才能控制垂直运动。
   若两者均设为 `NAN`，机体将保持当前高度。
2. 对于横向输入：`course`、`airspeed_direction` 或 `lateral_acceleration` 至少有一个为有效值。

关于可控参数的详细说明，请参阅消息定义（[FixedWingLateralSetpoint](../msg_docs/FixedWingLateralSetpoint.md) 和 [FixedWingLongitudinalSetpoint](../msg_docs/FixedWingLongitudinalSetpoint.md)）。

##### 基本用法

此类包含多个更新方法，每个方法允许您指定越来越多的设定值。

最简单的方法是 `updateWithAltitude()`，它允许您指定 `航向` 和 `高度` 目标设定值：

```cpp
const float altitude_msl = 500.F;
const float course = 0.F; // 正北方向
_fw_lateral_longitudinal_setpoint->updateWithAltitude(altitude_msl, course);
```

PX4 使用这些设定值计算 _滚转角_、_俯仰角_ 和 _油门_ 设定值，然后将其发送给底层控制器。
请注意，使用此方法时，预期的飞行指令应相对温和/不激进。
具体实现如下：

- 横向控制输出：

  航向设定值（由用户设定）&rarr; 空速方向（航向）设定值 &rarr; 横向加速度设定值 &rarr; 滚转角设定值。

- 纵向控制输出：

  高度设定值（由用户设定）&rarr; 高度变化率设定值 &rarr; 俯仰角设定值和油门设置。

`updateWithHeightRate()` 方法允许您设置目标 `航向` 和 `height_rate`（这在需要控制上升或下降速度或需要动态控制时很有用）：

```cpp
const float height_rate = 2.F;
const float course = 0.F; // 正北方向
_fw_lateral_longitudinal_setpoint->updateWithHeightRate(height_rate, course);
```

`updateWithAltitude()` 和 `updateWithHeightRate()` 方法还可以通过分别指定第三个和第四个参数来控制等效空速或横向加速度：

```cpp
const float altitude_msl = 500.F;
const float course = 0.F; // 正北方向
const float equivalent_aspd = 15.F; // m/s
const float lateral_acceleration = 2.F; // FRD，用作前馈控制

_fw_lateral_longitudinal_setpoint->updateWithAltitude(altitude_msl,
  course,
  equivalent_aspd,
  lateral_acceleration);
```

等效空速和横向加速度参数被定义为 `std::optional<float>`，因此您可以通过传递 `std::nullopt` 来省略任意一个参数。

::: tip
如果同时提供了横向加速度和航向设定值，横向加速度设定值将被用作前馈控制。
:::

##### 使用设定点结构实现完全控制

为了获得最大灵活性，您可以创建并传递一个 [`FwLateralLongitudinalSetpoint`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1FwLateralLongitudinalSetpoint.html) 结构体。每个字段均使用 `std::optional<float>` 模板化。

::: tip
如果同时设置航向和空速方向：空速方向具有优先级，航向将不被控制。  
如果同时设置横向加速度和航向/空速方向：横向加速度视为前馈，航向/空速方向为有限值。  
如果同时设置高度和高度率：高度率具有优先级，高度将不被控制。  
:::

```cpp
px4_ros2::FwLateralLongitudinalSetpoint setpoint_s;

setpoint_s.withCourse(0.F);
// setpoint_s.withAirspeedDirection(0.2F); // uncontrolled
setpoint_s.withLateralAcceleration(2.F); // feedforward
//setpoint_s.withAltitude(500.F); // uncontrolled
setpoint_s.withHeightRate(2.F);
setpoint_s.withEquivalentAirspeed(15.F);

_fw_lateral_longitudinal_setpoint->update(setpoint_s);
```

下图展示了当所有输入设置时，`FwLateralLongitudinalSetpointType` 与 PX4 之间的交互关系。

![FW ROS Interaction](../../assets/middleware/ros2/px4_ros2_interface_lib/fw_lat_long_ros_interaction.svg)

##### 高级配置（可选）

您还可以将 [`FwControlConfiguration`](https://auterion.github.io/px4-ros2-interface-lib/structpx4__ros2_1_1FwControlConfiguration.html) 结构体与设定值一起传递，以覆盖默认控制器设置和约束，例如俯仰限制、油门限制和目标俯冲/爬升率。  
此功能面向高级用户：

```cpp
px4_ros2::FwLateralLongitudinalSetpoint setpoint_s;

setpoint_s.withAirspeedDirection(0.F);
setpoint_s.withLateralAcceleration(2.F); // feedforward
setpoint_s.withAltitude(500.F);
setpoint_s.withEquivalentAirspeed(15.F);

px4_ros2::FwControlConfiguration config_s;

config_s.withTargetClimbRate(3.F);
config_s.withMaxAcceleration(5.F);
config_s.withThrottleLimits(0.4F, 0.6F);

_fw_lateral_longitudinal_setpoint->update(setpoint_s, config_s);
```

所有配置字段均定义为 `std::optional<float>`。  
未设置的值将默认采用 PX4 配置。  
有关配置选项的更多信息，请参见 [LateralControlConfiguration](../msg_docs/LateralControlConfiguration.md) 和 [FixedWingLongitudinalConfiguration](../msg_docs/LongitudinalControlConfiguration.md)。

:::info
出于安全考虑，PX4 会自动将配置值限制在机体约束范围内。  
例如，油门覆盖值会被限制在 [`FW_THR_MIN`](../advanced_config/parameter_reference.md#FW_THR_MIN)  
和 [`FW_THR_MAX`](../advanced_config/parameter_reference.md#FW_THR_MAX) 之间。
:::

#### 直接执行器控制设定点（DirectActuatorsSetpointType）

执行器可通过[px4_ros2::DirectActuatorsSetpointType](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1DirectActuatorsSetpointType.html)设定点类型直接控制。  
电机和舵机可以独立设置。请注意，分配方式是机体和配置特定的。例如，控制四旋翼飞行器时，需要根据其[输出配置](../concept/control_allocation.md)设置前4个电机。

::: info
如果要控制不控制机体运动的执行器（例如有效载荷舵机），请参见[下方](#controlling-an-independent-actuator-servo)。

### 控制 VTOL

<Badge type="tip" text="main (planned for: PX4 v1.17)" /> <Badge type="warning" text="Experimental" />

在外部飞行模式中控制 VTOL 时，需要根据当前飞行配置返回正确的设定点类型：

- 多旋翼模式：使用与多旋翼控制兼容的设定点类型。例如：`GotoSetpointType` 或 `TrajectorySetpointType`。
- 固定翼模式：使用 `FwLateralLongitudinalSetpointType`。

只要 VTOL 在外部模式下持续保持多旋翼或固定翼模式，无需额外处理。

如果需要在外部模式中命令 VTOL 转换，需要使用 [VTOL API](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1VTOL.html)。VTOL API 提供了命令转换和查询机体当前状态的功能。

请谨慎使用此 API！
外部命令转换会使用户部分负责确保平滑和安全的行为，这与机载转换（例如通过遥控器开关）不同，在机载转换中 PX4 会处理整个过程：

1. 确保您的模式中可用 `TrajectorySetpointType` 和 `FwLateralLongitudinalSetpointType`。
2. 在模式的构造函数中创建一个 `px4_ros2::VTOL` 实例。
3. 要命令转换，可以在 VTOL 对象上使用 `toMulticopter()` 或 `toFixedwing()` 方法设置所需状态。
4. 在转换期间发送以下组合的设定点：

   ```cpp
   // 假设 px4_ros2::VTOL 对象的实例名为 vtol

   // 以如下方式发送 TrajectorySetpointType：
   Eigen::Vector3f acceleration_sp = vtol.computeAccelerationSetpointDuringTransition();
   Eigen::Vector3f velocity_sp{NAN, NAN, 0.f};

   _trajectory_setpoint->update(velocity_sp, acceleration_sp);

   // 发送 FwLateralLongitudinalSetpointType，使用横向输入重新对准机体

   float course_sp = 0.F; // 北向

   _fw_lateral_longitudinal_setpoint->updateWithAltitude(NAN, course_sp)
   ```

   这将确保转换在 PX4 中得到正确处理。
   可选地，向 `computeAccelerationSetpointDuringTransition()` 传递一个减速设定点，用于反向转换期间使用。

要检查机体的当前状态，请在 `px4_ros2::VTOL` 对象上使用 `getCurrentState()` 方法。

查看 [此外部飞行模式实现](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/examples/cpp/modes/vtol) 以了解如何使用此 API 的实际示例。

### 控制独立执行器/舵机

如果需要控制独立执行器（舵机），请按照以下步骤操作：

1. [配置输出](../payloads/generic_actuator_control.md#generic-actuator-control-with-mavlink)。
2. 在模式的构造函数中创建 [px4_ros2::PeripheralActuatorControls](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1PeripheralActuatorControls.html) 的实例。
3. 调用 `set()` 方法来控制执行器。
   此操作可以独立于任何活动的设定值执行。

### 遥测

您可以直接通过以下类访问 PX4 遥测话题：

- [OdometryGlobalPosition](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1OdometryGlobalPosition.html)：全局位置
- [OdometryLocalPosition](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1OdometryLocalPosition.html)：局部位置、速度、加速度和航向
- [OdometryAttitude](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1OdometryAttitude.html)：机体姿态
- [OdometryAirspeed](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1OdometryAirspeed.html)：空速

例如，您可以按如下方式查询机体的当前位置估计值：

```cpp
std::shared_ptr<px4_ros2::OdometryLocalPosition> _vehicle_local_position;
...

// 获取机体的最后局部位置
_vehicle_local_position->positionNed();

// 检查最后水平位置是否有效
_vehicle_local_position->positionXYValid();
```

::: info
这些话题为内部 PX4 话题提供了封装，允许库在内部话题变更时保持兼容性。
检查 [px4_ros2/odometry](https://github.com/Auterion/px4-ros2-interface-lib/tree/main/px4_ros2_cpp/include/px4_ros2/odometry) 以获取新话题，当然您也可以使用 PX4 发布的任何 ROS 2 话题。
:::

### 故障安全机制和模式要求  

每个模式都具有一组要求标志。  
这些标志通常会根据模式上下文中使用了哪些对象而自动设置。  
例如，当使用以下代码添加手动控制输入时，手动控制的要求标志会自动设置：  

```cpp
_manual_control_input = std::make_shared<px4_ros2::ManualControlInput>(*this);
```  

具体而言，如果未满足条件，PX4 中设置标志会产生以下后果：  

- 在模式被选中时，不允许上锁（arming）  
- 若已上锁，则无法选择该模式  
- 若已上锁且选择了该模式，则会触发相应的故障安全机制（例如，当手动控制要求缺失时，触发遥控器信号丢失）。  
  有关如何配置故障安全行为，请查看 [安全页面](../config/safety.md)。  
  当模式崩溃或无响应时，同样会触发故障安全机制。  

以下是手动控制标志对应的流程图：  

![模式要求示意图](../../assets/middleware/ros2/px4_ros2_interface_lib/mode_requirements_diagram.png)  

<!-- 来源: https://drive.google.com/file/d/1g_NlQlw7ROLP_mAi9YY2nDwP0zTNsFlB/view -->  

在模式注册后，可以手动更新任何模式要求。  
例如，将“家点位置”添加为要求：  

```cpp
modeRequirements().home_position = true;
```  

所有标志的完整列表可参考 [requirement_flags.hpp](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/px4_ros2_cpp/include/px4_ros2/common/requirement_flags.hpp)。

#### 延迟故障安全

模式或模式执行器可以通过调用方法 [`deferFailsafesSync()`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1ModeExecutorBase.html#a16ec5be6ebe70e1d0625bf696c3e29ae) 暂时延迟非关键故障安全。
要获取故障安全触发的通知，需要重写方法 [`void onFailsafeDeferred()`](https://auterion.github.io/px4-ros2-interface-lib/classpx4__ros2_1_1ModeExecutorBase.html#ad80a234c8cb2f4c186fa2b7bffd1a1dd)。

请参考 [集成测试](https://github.com/Auterion/px4-ros2-interface-lib/blob/main/px4_ros2_cpp/test/integration/overrides.cpp) 获取示例。

### 将模式分配给遥控开关或操纵杆动作

外部模式可以分配到[遥控开关](../config/flight_mode.md)或操纵杆动作。
在将模式分配给遥控开关时，需要知道索引（因为参数元数据不包含动态模式名称）。
在模式运行时使用 `commander status` 命令获取该信息。

例如：

```
   INFO  [commander] 外部模式1: nav_state: 23, name: My Manual Mode
```

表示您将在QGC中选择 **External Mode 1**：

![QGC模式分配](../../assets/middleware/ros2/px4_ros2_interface_lib/qgc_mode_assignment.png)

::: info
PX4通过存储模式名称的哈希值，确保给定模式始终分配到相同的索引。
这使得在存在多个外部模式时，与启动顺序无关。
:::

### 替换内部模式

外部模式可以替换现有的内部模式，例如[返回](../flight_modes/return.md)模式(RTL)。  
通过这种方式，当RTL被选中（通过用户操作或安全机制触发时），将使用外部模式替代内部模式。  
内部模式仅在外部模式无响应或崩溃时作为备用方案使用。

替换模式可以在`ModeBase`构造函数的设置中指定：  

```cpp
Settings{kName, false, ModeBase::kModeIDRtl}
```