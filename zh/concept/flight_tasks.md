# 飞行任务

_Flight Tasks_ 用于 [Flight Modes](../concept/flight_modes.md) 中提供特定的运动行为：例如 follow me，或 flight smoothing。

## 概述

飞行任务是飞行任务框架中的一个类，它继承自基础类 [FlightTask](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/flight_mode_manager/tasks/FlightTask/FlightTask.hpp)。其目标是根据任意输入数据为控制器生成设定点，其中每个任务为特定模式实现期望的机体行为。

开发者通常通过调用基础任务的最小实现并扩展所需行为的实现来覆盖 `activate()` 和 `update()` 虚方法。`activate()` 方法在切换到该任务时被调用，允许初始化其状态，并从先前任务应用的设定点平滑接管。

`update()` 在每次执行循环迭代时被调用，包含生成设定点的核心行为实现。

按照惯例，任务存储在 [PX4-Autopilot/src/modules/flight_mode_manager/tasks](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/flight_mode_manager/tasks) 子文件夹中，文件夹名称与任务同名，源文件以 "FlightTask" 为前缀命名。

::: info
PX4 开发者峰会的视频概述见下方 [视频](#视频)。
:::

## 创建飞行任务

以下说明可用于创建名为 _MyTask_ 的任务:

1. 在 [PX4-Autopilot/src/modules/flight_mode_manager/tasks](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/flight_mode_manager/tasks) 中为新飞行任务创建目录。  
   按惯例目录名与任务同名，因此我们将其命名为 **/MyTask**。

   ```sh
   mkdir PX4-Autopilot/src/modules/flight_mode_manager/tasks/MyTask
   ```

2. 在 _MyTask_ 目录中使用前缀 "FlightTask" 为新飞行任务创建空的源代码和 _cmake_ 文件：  
   - CMakeLists.txt  
   - FlightTaskMyTask.hpp  
   - FlightTaskMyTask.cpp  

3. 更新 **CMakeLists.txt** 用于新任务  
   - 复制其他任务的 **CMakeLists.txt** 内容，例如 [Orbit/CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/flight_mode_manager/tasks/Orbit/CMakeLists.txt)  
   - 更新版权信息为当前年份  

     ```cmake
     ############################################################################
     #
     #   Copyright (c) 2021 PX4 Development Team. All rights reserved.
     #
     ```

   - 修改代码以反映新任务 - 例如将 `FlightTaskOrbit` 替换为 `FlightTaskMyTask`。  
     代码大致如下：

     ```cmake
     px4_add_library(FlightTaskMyTask
         FlightTaskMyTask.cpp
     )

     target_link_libraries(FlightTaskMyTask PUBLIC FlightTask)
     target_include_directories(FlightTaskMyTask PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
     ```

4. 更新头文件（此处为 **FlightTaskMyTask.hpp**）：  
   大多数任务会重新实现虚拟方法 `activate()` 和 `update()`，此示例还有一个私有变量。

   ```cpp
   #pragma once

   #include "FlightTask.hpp"

   class FlightTaskMyTask : public FlightTask
   {
   public:
     FlightTaskMyTask() = default;
     virtual ~FlightTaskMyTask() = default;

     bool update();
     bool activate(const trajectory_setpoint_s &last_setpoint) override;

   private:
     float _origin_z{0.f};
   };
   ```

5. 按需更新 cpp 文件。  
   此示例提供了 **FlightTaskMyTask.cpp** 的简单实现，仅指示任务方法被调用。

   ```cpp
   #include "FlightTaskMyTask.hpp"

   bool FlightTaskMyTask::activate(const trajectory_setpoint_s &last_setpoint)
   {
     bool ret = FlightTask::activate(last_setpoint);
     PX4_INFO("FlightTaskMyTask activate was called! ret: %d", ret); // 报告激活是否成功
     return ret;
   }

   bool FlightTaskMyTask::update()
   {
     PX4_INFO("FlightTaskMyTask update was called!"); // 报告更新
     return true;
   }
   ```

6. 将新任务添加到 [PX4-Autopilot/src/modules/flight_mode_manager/CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/flight_mode_manager/CMakeLists.txt#L41) 中的构建任务列表。

   ```cmake
   ...
    list(APPEND flight_tasks_all
      Auto
      Descend
      ...
      ManualPositionSmoothVel
      Transition
      MyTask
    )
   ...
   ```

   ::: tip

   上面添加的任务将在所有开发板上构建，包括像 Pixhawk FMUv2 这类闪存受限的开发板。  
   如果你的任务不适用于闪存受限的开发板，应将其添加到下方的条件代码块中（如示例所示）。

   ```cmake
   ...
   if(NOT px4_constrained_flash_build)
      list(APPEND flight_tasks_all
        AutoFollowTarget
        Orbit
        MyTask
      )
    endif()
   ...
   ```

   :::

7. 更新飞行模式以确保任务被调用。  
   通常通过参数选择特定飞行任务的启用时机。

   例如，要在多旋翼 Position 模式中启用新的 `MyTask`：  
   - 更新 `MPC_POS_MODE` ([multicopter_position_mode_params.c](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mc_pos_control/multicopter_position_mode_params.c))，添加选择 "MyTask" 的选项（如果参数值未被使用，比如5）：

     ```c
     ...
      * @value 0 Direct velocity
      * @value 3 Smoothed velocity
      * @value 4 Acceleration based
      * @value 5 My task
      * @group Multicopter Position Control
      */
     PARAM_DEFINE_INT32(MPC_POS_MODE, 5);
     ```

   - 在 [FlightModeManager.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/flight_mode_manager/FlightModeManager.cpp#L266-L285) 的参数 switch 语句中添加新选项，当 `_param_mpc_pos_mode` 具有相应值时启用任务。

     ```cpp
     ...
     // manual position control
     ...
     switch (_param_mpc_pos_mode.get()) {
       ...
       case 3:
          error = switchTask(FlightTaskIndex::ManualPositionSmoothVel);
          break;
       case 5: // 添加新任务的 case: MyTask
          error = switchTask(FlightTaskIndex::MyTask);
          break;
     case 4:
     ....
     ...
     ```

## 测试新飞行任务

要测试飞行任务，需要在启用该任务的情况下运行机体。  
对于上述示例，这意味着将参数 `MPC_POS_MODE` 设置为 5，起飞并切换机体到 [位置模式](../flight_modes_mc/position.md)。

::: info
上述任务应仅在模拟器中测试。  
代码实际上并未生成设定点，因此机体将无法飞行。
:::

构建 SITL 仿真（gazebo-classic）

```sh
make px4_sitl gazebo-classic
```

打开 QGroundControl（若未开启，将不会打印信息）。  
在控制台中执行起飞并切换到位置模式：

```sh
pxh> commander takeoff
pxh> commander mode posctl
```

控制台将持续显示：`INFO [FlightTaskMyTask] FlightTaskMyTask update was called!`  
若需要切换其他飞行模式，可以输入命令更改模式，例如 `commander mode altctl`。

## 视频

以下视频提供了PX4中飞行任务的概述。  
第一个视频涵盖了PX4 v1.9版本中飞行任务框架的状态。  
第二个视频是更新内容，涵盖了PX4 v1.11版本的变更。  

### PX4飞行任务架构概述（PX4开发者峰会2019）  

Dennis Mannhart和Matthias Grob讲解了PX4 v1.9中飞行模式的实现方式。  

<lite-youtube videoid="-dkQG8YLffc" title="PX4 Flight Task Architecture Overview"/>

<!-- datestamp:video:youtube:20190704:PX4 Flight Task Architecture Overview — PX4 Developer Summit 2019 -->  

### 多旋翼控制从传感器到电机的概述（PX4开发者峰会虚拟会议2020）  

<lite-youtube videoid="orvng_11ngQ" params="start=560" title="Overview of multicopter control from sensors to motors"/>

<!-- datestamp:video:youtube:20200720:Overview of multicopter control from sensors to motors — PX4 Developer Summit Virtual 2020 From 9min20sec - Section on flight tasks-->  

该视频的相关部分更新了PX4 v11.1中飞行任务的内容（9分20秒处）。  
[PPT请见此处（PDF）](https://static.sched.com/hosted_files/px4developersummitvirtual2020/1b/PX4%20Developer%20Summit%202020%20-%20Overview%20of%20multicopter%20control%20from%20sensors%20to%20motors.pdf) - 第9和第12页内容相关。