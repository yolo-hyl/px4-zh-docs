# 使用 MAVSDK 进行集成测试

PX4 可以通过基于 [MAVSDK](https://mavsdk.mavlink.io) 的集成测试进行端到端测试。

这些测试主要针对软件在环仿真(SITL)开发，并在持续集成(CI)中运行。未来我们计划将其推广到任何平台/硬件。

以下说明解释了如何在本地设置和运行测试。

:::note
这是 PX4 推荐的集成测试框架。
:::

## 先决条件

### 设置开发环境

如果您尚未操作：

- 为 [Linux](../dev_setup/dev_env_linux_ubuntu.md) 或 [macOS](../dev_setup/dev_env_mac.md) 安装开发工具链（不支持 Windows）。
  [Gazebo Classic](../sim_gazebo_classic/index.md) 是必需的，应默认安装。
- [获取 PX4 源代码](../dev_setup/building_px4.md#download-the-px4-source-code):

  ```sh
  git clone https://github.com/PX4/PX4-Autopilot.git --recursive
  cd PX4-Autopilot
  ```

### 为测试构建 PX4

要构建用于仿真测试的 PX4 源代码，请使用：

```sh
DONT_RUN=1 make px4_sitl gazebo-classic mavsdk_tests
```

### 安装 MAVSDK C++ 库

测试需要系统范围内安装 MAVSDK C++ 库（例如在 `/usr/lib` 或 `/usr/local/lib` 中）。

可以从二进制或源代码安装：

- [MAVSDK > C++ > C++ 快速入门](https://mavsdk.mavlink.io/main/en/cpp/quickstart.html): 在支持的平台上安装预构建库（推荐）
- [MAVSDK > C++ 指南 > 从源代码构建](https://mavsdk.mavlink.io/main/en/cpp/guide/build.html): 从源代码构建 C++ 库

## 运行所有 PX4 测试

要运行 [sitl.json](https://github.com/PX4/PX4-Autopilot/blob/main/test/mavsdk_tests/configs/sitl.json) 中定义的所有 SITL 测试，请执行：

```sh
test/mavsdk_tests/mavsdk_test_runner.py test/mavsdk_tests/configs/sitl.json --speed-factor 10
```

这将列出所有测试并按顺序运行它们。

要查看所有可能的命令行参数，请使用 `-h` 参数：

```sh
test/mavsdk_tests/mavsdk_test_runner.py -h

usage: mavsdk_test_runner.py [-h] [--log-dir LOG_DIR] [--speed-factor SPEED_FACTOR] [--iterations ITERATIONS] [--abort-early]
                             [--gui] [--model MODEL] [--case CASE] [--debugger DEBUGGER] [--upload] [--force-color]
                             [--verbose] [--build-dir BUILD_DIR]

positional arguments:
  config_file           JSON config file to use

options:
  -h, --help            show this help message and exit
  --log-dir LOG_DIR     Directory for log files
  --speed-factor SPEED_FACTOR
                        how fast to run the simulation
  --iterations ITERATIONS
                        how often to run all tests
  --abort-early         abort on first unsuccessful test
  --gui                 display the visualization for a simulation
  --model MODEL         only run tests for one model
  --case CASE           only run tests for one case (or multiple cases with wildcard '*')
  --debugger DEBUGGER   choice from valgrind, callgrind, gdb, lldb
  --upload              Upload logs to logs.px4.io
  --force-color         Force colorized output
  --verbose             enable more verbose output
  --build-dir BUILD_DIR
                        relative path where the built files are stored
```

## 运行单个测试

通过指定 `model` 和测试 `case` 作为命令行选项来运行单个测试。
例如，测试飞行器在任务中飞行时，可以运行：

```sh
test/mavsdk_tests/mavsdk_test_runner.py test/mavsdk_tests/configs/sitl.json --speed-factor 10 --model tailsitter --case 'Fly VTOL mission'
```

找出当前模型和其相关测试用例的最简单方法是运行所有 PX4 测试 [如上所述](#运行所有 PX4 测试)（注意，如果只想测试一个测试用例，可以随时取消构建）。

在写作时，通过运行所有测试生成的列表为：

```sh
About to run 39 test cases for 3 selected models (1 iteration):
  - iris:
    - 'Land on GPS lost during mission (baro height mode)'
    - 'Land on GPS lost during mission (GPS height mode)'
    - 'Continue on mag lost during mission'
    - 'Continue on baro lost during mission (baro height mode)'
    - 'Continue on baro lost during mission (GPS height mode)'
    - 'Continue on baro stuck during mission (baro height mode)'
    - 'Continue on baro stuck during mission (GPS height mode)'
    - 'Takeoff and Land'
    - 'Fly square Multicopter Missions including RTL'
    - 'Fly square Multicopter Missions with manual RTL'
    - 'Fly straight Multicopter Mission'
    - 'Offboard takeoff and land'
    - 'Offboard position control'
    - 'Fly forward in position control'
    - 'Fly forward in altitude control'
  - standard_vtol:
    - 'Land on GPS lost during mission (baro height mode)'
    - 'Land on GPS lost during mission (GPS height mode)'
    - 'Continue on mag lost during mission'
    - 'Continue on baro lost during mission (baro height mode)'
    - 'Continue on baro lost during mission (GPS height mode)'
    - 'Continue on baro stuck during mission (baro height mode)'
    - 'Continue on baro stuck during mission (GPS height mode)'
    - 'Takeoff and Land'
    - 'Fly square Multicopter Missions including RTL'
    - 'Fly square Multicopter Missions with manual RTL'
    - 'Fly straight Multicopter Mission'
    - 'Offboard takeoff and land'
    - 'Offboard position control'
    - 'Fly forward in position control'
    - 'Fly forward in altitude control'
  - tailsitter:
    - 'Land on GPS lost during mission (baro height mode)'
    - 'Land on GPS lost during mission (GPS height mode)'
    - 'Continue on mag lost during mission'
    - 'Continue on baro lost during mission (baro height mode)'
    - 'Continue on baro lost during mission (GPS height mode)'
    - 'Continue on baro stuck during mission (baro height mode)'
    - 'Continue on baro stuck during mission (GPS height mode)'
    - 'Takeoff and Land'
    - 'Fly square Multicopter Missions including RTL'
    - 'Fly square Multicopter Missions with manual RTL'
    - 'Fly straight Multicopter Mission'
    - 'Offboard takeoff and land'
    - 'Offboard position control'
    - 'Fly forward in position control'
    - 'Fly forward in altitude control'
```

## 术语说明

- "model": 这是选定的 Gazebo 模型，例如 `iris`。
- "test case": 这是一个 [catch2 测试用例](https://github.com/catchorg/Catch2/blob/master/docs/test-cases-and-sections.md)。