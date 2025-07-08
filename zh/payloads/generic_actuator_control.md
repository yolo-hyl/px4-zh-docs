# 通用执行器控制

您可以将任意硬件连接到未使用的 PX4 输出，并通过 [RC 控制](#generic-actuator-control-with-rc) 或 [MAVLink](#generic-actuator-control-with-mavlink)（作为指令或在 [任务](#generic-actuator-control-in-missions) 中）进行控制。

当需要使用没有对应 MAVLink 指令的载荷类型，或 PX4 没有特定集成时，这种功能非常有用。

::: info
在可能的情况下，优先使用集成硬件和硬件特定的 MAVLink 指令（如 [Grippers](../peripherals/gripper.md)），而不是通用执行器控制。
使用集成硬件可以优化 [任务规划和行为](../flying/package_delivery_mission.md)，因为任务可以了解硬件的关键信息（例如触发所需的时间）。
:::

## 使用MAVLink的通用执行器控制

[MAV_CMD_DO_SET_ACTUATOR](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ACTUATOR) 可用于同时设置最多6个执行器的值。该指令既可以通过创建"设置执行器"任务项在[任务](#generic-actuator-control-in-missions)中使用，也可以作为独立指令使用。

在[执行器](../config/actuators.md#actuator-outputs)配置界面中，通过将 `Peripheral via Actuator Set 1` 到 `Peripheral via Actuator Set 6` 功能分配到目标[执行器输出](../config/actuators.md#actuator-outputs)来指定要控制的输出。

![QGC中的通用执行器输出设置示例](../../assets/peripherals/qgc_generic_actuator_output_setting_example.png)

`MAV_CMD_DO_SET_ACTUATOR` 的 `param1` 到 `param6` 分别控制由 `Peripheral via Actuator Set 1` 到 `Peripheral via Actuator Set 6` 映射的输出。

例如，在上图中，`AUX5` 输出被分配了 `Peripheral via Actuator Set 1` 功能。要控制连接到 `AUX5` 的执行器，需要设置 `MAV_CMD_DO_SET_ACTUATOR.param1` 的值。

<!-- PX4 v1.14 bug https://github.com/PX4/PX4-Autopilot/issues/21966 -->## 使用RC进行通用执行器控制

最多可使用6个自动驾驶仪PWM或CAN输出通过RC通道进行控制。
需要控制的输出在[Actuators](../config/actuators.md#actuator-outputs)配置界面中设置，通过将功能`RC AUX 1`至`RC AUX 6`分配给目标[actuator outputs](../config/actuators.md#actuator-outputs)来实现。

要将特定RC通道映射到输出功能`RC AUX n`（从而控制对应输出），需使用参数[RC_MAP_AUXn](../advanced_config/parameter_reference.md#RC_MAP_AUX1)，其中`n`与功能编号相同。

例如，要控制连接到AUX 3引脚的执行器，可将输出功能`RC AUX 5`分配给输出`AUX3`。
随后可通过设置`RC_MAP_AUX5`参数来控制`AUX3`输出。

## 任务中的通用执行器控制

要在任务中使用通用执行器控制，首先需要[配置您希望通过MAVLink控制的输出](#generic-actuator-control-with-mavlink)。

然后在 _QGroundControl_ 中，可以通过 **设置执行器** 任务项来设置任务中的执行器输出值（这会向上传的任务计划中添加一个 [MAV_CMD_DO_SET_ACTUATOR](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ACTUATOR) 指令）。

需要注意的是，使用通用执行器控制时，既 _QGroundControl_ 也不 PX4 会了解触发的硬件情况。
在处理任务项时，PX4 会简单地将输出设置为指定值，然后立即继续执行下一个任务项。
如果硬件需要时间激活且您需要在当前航点暂停以完成此操作，则需要通过添加额外任务项来规划任务以实现所需行为。

::: info
这就是为何推荐使用集成硬件的原因之一！
它允许以通用方式编写任务，任何硬件特定行为或时序都可以通过飞行栈配置进行管理。
:::

在任务中使用通用执行器的步骤如下：

1. 创建一个任务项，指定您希望执行器指令的位置。
1. 将航点任务项更改为 **设置执行器** 任务项：

   ![设置执行器任务项](../../assets/qgc/plan/mission_item_editors/mission_item_select_set_actuator.png)

   - 选择航点任务编辑器中的标题以打开 **选择任务指令** 编辑器。
   - 选择 **高级** 分类，然后选择 **设置执行器** 项（如果未显示此选项，请尝试使用更新版本的 _QGroundControl_ 或每日构建版）。
     这将把任务项类型更改为 **设置执行器**。

1. 选择已连接的执行器并设置它们的值（这些值在 -1 到 1 之间归一化）。

   ![设置执行器任务项](../../assets/qgc/plan/mission_item_editors/set_actuator.png)

## MAVSDK (示例脚本)

以下[MAVSDK](https://mavsdk.mavlink.io/main/en/index.html) [示例代码](https://github.com/mavlink/MAVSDK/blob/main/examples/set_actuator/set_actuator.cpp)展示了如何使用MAVSDK Action插件的[`set_actuator()`](https://mavsdk.mavlink.io/main/en/cpp/api_reference/classmavsdk_1_1_action.html#classmavsdk_1_1_action_1ad30beac27f05c62dcf6a3d0928b86e4c)方法触发载荷释放。

`set_actuator()`的索引值对应你机体系结构中定义的MAVLink载荷输出。

::: info
MAVSDK在内部发送[MAV_CMD_DO_SET_ACTUATOR](https://mavlink.io/en/messages/common.html#MAV_CMD_DO_SET_ACTUATOR) MAVLink命令。
:::

```cpp
#include <mavsdk/mavsdk.h>
#include <mavsdk/plugins/action/action.h>
#include <chrono>
#include <cstdint>
#include <iostream>
#include <future>

using namespace mavsdk;

void usage(const std::string& bin_name)
{
    std::cerr << "Usage :" << bin_name << " <connection_url> <actuator_index> <actuator_value>\n"
              << "Connection URL format should be :\n"
              << " For TCP : tcp://[server_host][:server_port]\n"
              << " For UDP : udp://[bind_host][:bind_port]\n"
              << " For Serial : serial:///path/to/serial/dev[:baudrate]\n"
              << "For example, to connect to the simulator use URL: udp://:14540\n";
}

int main(int argc, char** argv)
{
    if (argc != 4) {
        usage(argv[0]);
        return 1;
    }

    const std::string connection_url = argv[1];
    const int index = std::stod(argv[2]);
    const float value = std::stof(argv[3]);

    Mavsdk mavsdk;
    const ConnectionResult connection_result = mavsdk.add_any_connection(connection_url);

    if (connection_result != ConnectionResult::Success) {
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    std::cout << "Waiting to discover system...\n";
    auto prom = std::promise<std::shared_ptr<System>>{};
    auto fut = prom.get_future();

    // We wait for new systems to be discovered, once we find one that has an
    // autopilot, we decide to use it.
    mavsdk.subscribe_on_new_system([&mavsdk, &prom]() {
        auto system = mavsdk.systems().back();

        if (system->has_autopilot()) {
            std::cout << "Discovered autopilot\n";

            // Unsubscribe again as we only want to find one system.
            mavsdk.subscribe_on_new_system(nullptr);
            prom.set_value(system);
        }
    });

    // We usually receive heartbeats at 1Hz, therefore we should find a
    // system after around 3 seconds max, surely.
    if (fut.wait_for(std::chrono::seconds(3)) == std::future_status::timeout) {
        std::cerr << "No autopilot found, exiting.\n";
        return 1;
    }

    // Get discovered system now.
    auto system = fut.get();

    // Instantiate plugins.
    auto action = Action{system};

    std::cout << "Setting actuator...\n";
    const Action::Result set_actuator_result = action.set_actuator(index, value);

    if (set_actuator_result != Action::Result::Success) {
        std::cerr << "Setting actuator failed:" << set_actuator_result << '\n';
        return 1;
    }

    return 0;
}
```

## 测试

由舵机和其他执行器（例如夹爪）触发的载荷可以在[预上锁状态](../getting_started/px4_basic_concepts.md#arming-and-disarming)下进行测试，该状态下会禁用电机但允许执行器移动。

这比机体处于上锁状态时测试更安全。

相机载荷可以在任何时间触发和测试。