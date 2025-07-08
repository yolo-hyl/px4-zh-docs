# 第一个应用程序教程 (Hello Sky)

本节解释如何创建和运行你的第一个机载应用程序。  
它涵盖了在PX4上进行应用程序开发所需的基本概念和API。  

::: info  
为了简化，省略了更高级的功能，如启动/停止功能和命令行参数。  
这些内容在 [Application/Module Template](../modules/module_template.md) 中有详细说明。  
:::

## 先决条件

您需要以下内容：

- [PX4 SITL Simulator](../simulation/index.md) _或_ [PX4-compatible flight controller](../flight_controller/index.md)
- 针对目标平台的[PX4 Development Toolchain](../dev_setup/dev_env.md)
- 从Github[下载PX4源代码](../dev_setup/building_px4.md#download-the-px4-source-code)

源代码目录[PX4-Autopilot/src/examples/px4_simple_app](https://github.com/PX4/PX4-Autopilot/tree/main/src/examples/px4_simple_app)包含本教程的完整版本，如果遇到困难可以参考该示例。

- 重命名（或删除）**px4_simple_app**目录。

## 最小应用

本节我们将创建一个**最小应用**，该应用仅输出 `Hello Sky!`。  
这包括一个单独的**C语言**文件和一个**cmake**定义文件（用于告知工具链如何构建该应用）。

1. 创建新目录 **PX4-Autopilot/src/examples/px4_simple_app**。  
1. 在该目录中创建名为 **px4_simple_app.c** 的新C文件：

   - 将默认头部复制到文件顶部。  
     所有贡献的代码文件都应包含此头部！

     ```c
     /****************************************************************************
      *
      *   Copyright (c) 2012-2022 PX4 Development Team. All rights reserved.
      *
      * Redistribution and use in source and binary forms, with or without
      * modification, are permitted provided that the following conditions
      * are met:
      *
      * 1. Redistributions of source code must retain the above copyright
      *    notice, this list of conditions and the following disclaimer.
      * 2. Redistributions in binary form must reproduce the above copyright
      *    notice, this list of conditions and the following disclaimer in
      *    the documentation and/or other materials provided with the
      *    distribution.
      * 3. Neither the name PX4 nor the names of its contributors may be
      *    used to endorse or promote products derived from this software
      *    without specific prior written permission.
      *
      * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
      * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
      * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
      * FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
      * COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
      * INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
      * BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
      * OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
      * AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
      * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
      * ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
      * POSSIBILITY OF SUCH DAMAGE.
      *
      ****************************************************************************/
     ```

   - 在默认头部下方复制以下代码。  
     所有贡献的代码文件都应包含此内容！

     ```c
     /**
      * @file px4_simple_app.c
      * Minimal application example for PX4 autopilot
      *
      * @author Example User <mail@example.com>
      */

     #include <px4_platform_common/log.h>

     __EXPORT int px4_simple_app_main(int argc, char *argv[]);

     int px4_simple_app_main(int argc, char *argv[])
     {
     	PX4_INFO("Hello Sky!");
     	return OK;
     }
     ```

     :::tip
     主函数必须命名为`<模块名>_main`并按照示例导出。
     :::

     :::tip
     `PX4_INFO` 是 PX4 shell 中 `printf` 的等效实现（包含于 **px4_platform_common/log.h** 中）。  
     提供了不同日志级别：`PX4_INFO`、`PX4_WARN`、`PX4_ERR`、`PX4_DEBUG`。  
     警告和错误信息会额外记录到 [ULog](../dev_log/ulog_file_format.md) 并显示在 [Flight Review](https://logs.px4.io/) 中。
     :::

1. 创建并打开名为 **CMakeLists.txt** 的新 _cmake_ 定义文件。  
   复制以下内容：

   ```cmake
   ############################################################################
   #
   #   Copyright (c) 2015 PX4 Development Team. All rights reserved.
   #
   # Redistribution and use in source and binary forms, with or without
   # modification, are permitted provided that the following conditions
   # are met:
   #
   # 1. Redistributions of source code must retain the above copyright
   #    notice, this list of conditions and the following disclaimer.
   # 2. Redistributions in binary form must reproduce the above copyright
   #    notice, this list of conditions and the following disclaimer in
   #    the documentation and/or other materials provided with the
   #    distribution.
   # 3. Neither the name PX4 nor the names of its contributors may be
   #    used to endorse or promote products derived from this software
   #    without specific prior written permission.
   #
   # THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
   # "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
   # LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
   # FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
   # COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
   # INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
   # BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
   # OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
   # AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
   # LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
   # ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
   # POSSIBILITY OF SUCH DAMAGE.
   #
   ############################################################################
   px4_add_module(
   	MODULE examples__px4_simple_app
   	MAIN px4_simple_app
   	STACK_MAIN 2000
   	SRCS
   		px4_simple_app.c
   	DEPENDS
   	)
   ```

   `px4_add_module()` 方法从模块描述构建静态库。

   - `MODULE` 块是固件中模块的唯一名称（按惯例，模块名称以回溯到 `src` 的父目录为前缀）。  
   - `MAIN` 块指定模块的入口点，该入口点会向 NuttX 注册命令，使其可通过 PX4 shell 或 SITL 控制台调用。

   :::tip
   `px4_add_module()` 的格式文档见 [PX4-Autopilot/cmake/px4_add_module.cmake](https://github.com/PX4/PX4-Autopilot/blob/main/cmake/px4_add_module.cmake)。
   :::

   ::: info
   如果向 `px4_add_module` 指定 `DYNAMIC` 选项，则在 POSIX 平台上会创建**共享库**（无需重新编译 PX4 即可加载，且可作为二进制文件而非源代码分发）。  
   你的应用将不会成为内置命令，而是保存为单独的文件 `examples__px4_simple_app.px4mod`。  
   可通过 `px4_add_module` 调用命令，例如：
   :::

1. 创建并打开名为 **Kconfig** 的新配置文件。  
   复制以下内容：

   ```
   menuconfig PX4_SIMPLE_APP
   	bool "Example simple app"
   	default n
   	help
   	  This is an example of a simple app.
   ```

## 构建应用程序/固件

应用程序现在已编写完毕。  
要运行它，首先需要确保其已作为PX4的一部分进行编译。  
应用程序通过目标平台对应的板级_px4board_文件添加到构建/固件中：

- PX4 SITL（模拟器）：[PX4-Autopilot/boards/px4/sitl/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/sitl/default.px4board)  
- Pixhawk v1/2：[PX4-Autopilot/boards/px4/fmu-v2/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v2/default.px4board)  
- Pixracer (px4/fmu-v4)：[PX4-Autopilot/boards/px4/fmu-v4/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v4/default.px4board)  
- 其他板卡的_px4board_文件可参考[PX4-Autopilot/boards/](https://github.com/PX4/PX4-Autopilot/tree/main/boards)

要启用将应用程序编译到固件中，可在_px4board_文件中添加对应的Kconfig键`CONFIG_EXAMPLES_PX4_SIMPLE_APP=y`，或运行[boardconfig](../hardware/porting_guide_config.md#px4-menuconfig-setup) `make px4_fmu-v4_default boardconfig`：

```
examples  --->
    [x] PX4 Simple app  ----
```

::: info  
大多数文件中该选项已默认存在，因为示例程序在固件中默认包含。  
:::

使用对应板卡的命令构建示例：

- jMAVSim 模拟器：`make px4_sitl_default jmavsim`  
- Pixhawk v1/2：`make px4_fmu-v2_default`（或直接使用 `make px4_fmu-v2`）  
- Pixhawk v3：`make px4_fmu-v4_default`  
- 其他板卡：[Building the Code](../dev_setup/building_px4.md#building-for-nuttx)

## 测试应用（硬件）

### 将固件上传到你的板载

启用上传程序，然后重置板载：

- Pixhawk v1/2: `make px4_fmu-v2_default upload`
- Pixhawk v3: `make px4_fmu-v4_default upload`

在重置板载之前，它应该打印出一些编译信息，并在最后显示：

```sh
Loaded firmware for X,X, waiting for the bootloader...
```

一旦板载被重置并上传，它会打印：

```sh
Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting.

[100%] Built target upload
```

### 连接到控制台

现在通过串口或USB连接到[系统控制台](../debug/system_console.md)。
按下 **ENTER** 键将显示shell提示符：

```sh
nsh>
```

输入 `help` 并按下 ENTER：

```sh
nsh> help
  help usage:  help [-v] [<cmd>]

  [           df          kill        mkfifo      ps          sleep
  ?           echo        losetup     mkrd        pwd         test
  cat         exec        ls          mh          rm          umount
  cd          exit        mb          mount       rmdir       unset
  cp          free        mkdir       mv          set         usleep
  dd          help        mkfatfs     mw          sh          xd

Builtin Apps:
  reboot
  perf
  top
  ..
  px4_simple_app
  ..
  sercon
  serdis
```

请注意 `px4_simple_app` 现在是可用命令的一部分。
通过输入 `px4_simple_app` 并按下 ENTER 来启动它：

```sh
nsh> px4_simple_app
Hello Sky!
```

该应用程序现已正确注册到系统中，可以扩展以执行实际有用的任务。

## 测试应用 (SITL)

如果你使用 SITL，_PX4 控制台_ 会自动启动（参见 [构建代码 > 首次构建（使用模拟器）](../dev_setup/building_px4.md#first-build-using-a-simulator)）。
与 _nsh 控制台_（见上一节）类似，你可以输入 `help` 查看内置应用列表。

输入 `px4_simple_app` 运行最小应用：

```sh
pxh> px4_simple_app
INFO  [px4_simple_app] Hello Sky!
```

该应用现在可以扩展以执行实际的有用任务。

## 订阅传感器数据

要实现功能，应用程序需要订阅输入数据并发布输出数据（例如电机或舵机指令）。

:::tip
此处体现了PX4硬件抽象的优势！
无需以任何方式与传感器驱动交互，且当板载传感器更新时也无需更新应用程序。
:::

应用程序之间的独立消息通道称为[uORB主题](../middleware/uorb.md)。在本教程中，我们关注[SensorCombined](https://github.com/PX4/PX4-Autopilot/blob/main/msg/SensorCombined.msg)主题，该主题包含整个系统的同步传感器数据。

订阅主题的操作非常简单：

```cpp
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
```

`sensor_sub_fd`是主题句柄，可用于高效阻塞等待新数据。当前线程在等待时会休眠，当新数据可用时由调度器自动唤醒，等待期间不会消耗CPU周期。为此我们使用[poll()](http://pubs.opengroup.org/onlinepubs/007908799/xsh/poll.html) POSIX系统调用。

在订阅中添加`poll()`如下所示（_伪代码，完整实现请参见下方_）：

```cpp
#include <poll.h>
#include <uORB/topics/sensor_combined.h>
..
int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));

/* 该技术可以同时等待多个主题，在此仅展示一个示例 */
px4_pollfd_struct_t fds[] = {
    { .fd = sensor_sub_fd,   .events = POLLIN },
};

while (true) {
	/* 等待1个文件描述符的传感器更新，超时1000毫秒（1秒） */
	int poll_ret = px4_poll(fds, 1, 1000);
	..
	if (fds[0].revents & POLLIN) {
		/* 获取第一个文件描述符的数据 */
		struct sensor_combined_s raw;
		/* 将传感器原始数据复制到本地缓冲区 */
		orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
		PX4_INFO("加速度计:\t%8.4f\t%8.4f\t%8.4f",
					(double)raw.accelerometer_m_s2[0],
					(double)raw.accelerometer_m_s2[1],
					(double)raw.accelerometer_m_s2[2]);
	}
}
```

通过以下命令重新编译应用程序：

```sh
make
```

### 测试uORB订阅

最后一步是在nsh shell中以后台进程/任务方式启动应用程序，输入以下命令：

```sh
px4_simple_app &
```

应用程序将在控制台显示5组传感器值后退出：

```sh
[px4_simple_app] 加速度计:   0.0483          0.0821          0.0332
[px4_simple_app] 加速度计:   0.0486          0.0820          0.0336
[px4_simple_app] 加速度计:   0.0487          0.0819          0.0327
[px4_simple_app] 加速度计:   0.0482          0.0818          0.0323
[px4_simple_app] 加速度计:   0.0482          0.0827          0.0331
[px4_simple_app] 加速度计:   0.0489          0.0804          0.0328
```

:::tip
[完整应用程序的模块模板](../modules/module_template.md)可用于编写可通过命令行控制的后台进程。
:::

## 发布数据

要使用计算输出结果，下一步是_发布_结果。  
以下是发布姿态主题的示例：

::: info
我们选择`attitude`是因为知道_mavlink_应用会将其转发到地面控制站 - 提供了一种查看结果的便捷方式。
:::

接口非常简单：初始化待发布主题的`struct`并声明主题：

```c
#include <uORB/topics/vehicle_attitude.h>
..
/* advertise attitude topic */
struct vehicle_attitude_s att;
memset(&att, 0, sizeof(att));
orb_advert_t att_pub_fd = orb_advertise(ORB_ID(vehicle_attitude), &att);
```

在主循环中，当数据就绪时发布信息：

```c
orb_publish(ORB_ID(vehicle_attitude), att_pub_fd, &att);
```

## 完整示例代码

[完整示例代码](https://github.com/PX4/PX4-Autopilot/blob/main/src/examples/px4_simple_app/px4_simple_app.c) 现在如下：

```c
/****************************************************************************
 *
 *   Copyright (c) 2012-2019 PX4 Development Team. All rights reserved.
 *
 * Redistribution and use in source and binary forms, with or without
 * modification, are permitted provided that the following conditions
 * are met:
 *
 * 1. Redistributions of source code must retain the above copyright
 *    notice, this list of conditions and the following disclaimer.
 * 2. Redistributions in binary form must reproduce the above copyright
 *    notice, this list of conditions and the following disclaimer in
 *    the documentation and/or other materials provided with the
 *    distribution.
 * 3. Neither the name PX4 nor the names of its contributors may be
 *    used to endorse or promote products derived from this software
 *    without specific prior written permission.
 *
 * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
 * "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
 * LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
 * FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
 * COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
 * INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
 * BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
 * OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
 * AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
 * LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
 * ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
 * POSSIBILITY OF SUCH DAMAGE.
 *
 ****************************************************************************/

/**
 * @file px4_simple_app.c
 * PX4自动驾驶仪的最小应用程序示例
 *
 * @author Example User <mail@example.com>
 */

#include <px4_platform_common/px4_config.h>
#include <px4_platform_common/tasks.h>
#include <px4_platform_common/posix.h>
#include <unistd.h>
#include <stdio.h>
#include <poll.h>
#include <string.h>
#include <math.h>

#include <uORB/uORB.h>
#include <uORB/topics/sensor_combined.h>
#include <uORB/topics/vehicle_attitude.h>

__EXPORT int px4_simple_app_main(int argc, char *argv[]);

int px4_simple_app_main(int argc, char *argv[])
{
	PX4_INFO("你好天空！");

	/* 订阅sensor_combined主题 */
	int sensor_sub_fd = orb_subscribe(ORB_ID(sensor_combined));
	/* 将更新频率限制为5 Hz */
	orb_set_interval(sensor_sub_fd, 200);

	/* 发布机体姿态主题 */
	struct vehicle_attitude_s att;
	memset(&att, 0, sizeof(att));
	orb_advert_t att_pub = orb_advertise(ORB_ID(vehicle_attitude), &att);

	/* 可以使用此技术等待多个主题，此处仅使用一个 */
	px4_pollfd_struct_t fds[] = {
		{ .fd = sensor_sub_fd,   .events = POLLIN },
		/* 此处可以添加更多文件描述符，格式如下：
		 * { .fd = other_sub_fd,   .events = POLLIN },
		 */
	};

	int error_counter = 0;

	for (int i = 0; i < 5; i++) {
		/* 等待传感器更新1个文件描述符，最多等待1000 ms（1秒） */
		int poll_ret = px4_poll(fds, 1, 1000);

		/* 处理轮询结果 */
		if (poll_ret == 0) {
			/* 表示没有任何提供者在1秒内提供数据 */
			PX4_ERR("1秒内未获取到数据");

		} else if (poll_ret < 0) {
			/* 这是非常严重的问题 - 应该是紧急情况 */
			if (error_counter < 10 || error_counter % 50 == 0) {
				/* 使用计数器防止洪水（并减缓我们） */
				PX4_ERR("poll() 返回错误值: %d", poll_ret);
			}

			error_counter++;

		} else {

			if (fds[0].revents & POLLIN) {
				/* 获取第一个文件描述符的数据 */
				struct sensor_combined_s raw;
				/* 将传感器原始数据复制到本地缓冲区 */
				orb_copy(ORB_ID(sensor_combined), sensor_sub_fd, &raw);
				PX4_INFO("加速度计:\t%8.4f\t%8.4f\t%8.4f",
					 (double)raw.accelerometer_m_s2[0],
					 (double)raw.accelerometer_m_s2[1],
					 (double)raw.accelerometer_m_s2[2]);

				/* 设置att并发布此信息供其他应用使用
				 以下没有实际意义，仅作为示例
				*/
				att.q[0] = raw.accelerometer_m_s2[0];
				att.q[1] = raw.accelerometer_m_s2[1];
				att.q[2] = raw.accelerometer_m_s2[2];

				orb_publish(ORB_ID(vehicle_attitude), att_pub, &att);
			}

			/* 此处可以添加更多文件描述符，格式如下:
			 * if (fds[1..n].revents & POLLIN) {}
			 */
		}
	}

	PX4_INFO("退出");

	return 0;
}
```

## 运行完整示例

最后运行你的应用程序：

```sh
px4_simple_app
```

如果你启动 _QGroundControl_，可以在实时图中检查传感器值（[分析 > MAVLink 检查器](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/analyze_view/mavlink_inspector.html)）。

## 总结

本教程涵盖了开发基本 PX4 自动驾驶仪应用程序所需的所有内容。  
请注意，完整的 uORB 消息/主题列表可[在此处获取](https://github.com/PX4/PX4-Autopilot/tree/main/msg/)，并且头部文件有良好的文档说明，可作为参考。

更多信息和故障排除/常见问题可在此处找到：[uORB](../middleware/uorb.md)。

下一页将展示一个包含启动和停止功能的完整应用程序模板。