# 单元测试

开发人员应尽可能在开发的各个阶段编写单元测试，包括添加新功能、修复缺陷和重构代码。

PX4 提供了多种编写单元测试的方法：

1. 使用 [Google Test](https://github.com/google/googletest/blob/main/docs/primer.md) ("GTest") 的单元测试 - 依赖项最少的内部测试  
1. 使用 GTest 的功能测试 - 依赖参数和 uORB 消息的测试  
1. SITL 单元测试。该方法适用于需要在完整 SITL 中运行的测试。这些测试运行速度较慢且调试难度更高，因此建议尽可能使用 GTest 替代。

## 编写 GTest 单元测试

**提示**: 通常情况下，如果你需要访问高级 GTest 工具、STL 数据结构，或需要链接到 `parameters` 或 `uorb` 库时，应改用功能测试。

创建新单元测试的步骤如下：

1. 单元测试应分为三个部分：初始化、运行、验证结果。每个测试应针对非常具体的某个行为或配置场景，这样当测试失败时可以明显看出问题所在。请尽可能遵循这些标准。
1. 将示例单元测试 [AttitudeControlTest](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mc_att_control/AttitudeControl/AttitudeControlTest.cpp) 复制并重命名到被测代码所在的目录中。
1. 将新文件添加到目录的 `CMakeLists.txt`。格式应类似 `px4_add_unit_gtest(SRC MyNewUnitTest.cpp LINKLIBS <library_to_be_tested>)`
1. 添加所需的测试功能。这需要包含特定测试所需的头文件，添加新测试（每个测试需有独立名称），并编写初始化逻辑、运行待测代码以及验证预期行为的代码。
1. 如果需要额外的库依赖，应在 `LINKLIBS` 后按照上述方式添加到 CMakeLists 中。

测试可通过 `make tests` 运行，之后你将在 `build/px4_sitl_test/unit-MyNewUnit` 中找到二进制文件。
它也可以直接在调试器中运行。

## 编写 GTest 功能测试

当测试或被测试组件依赖于参数、uORB 消息和/或高级 GTest 功能时，应使用 GTest 功能测试。
此外，功能测试可以包含本地使用的 STL 数据结构（尽管要注意不同平台之间的差异，例如 macOS 与 Linux）。

创建新功能测试的步骤如下：

1. 通常（与单元测试类似），功能测试应分为三个部分：初始化、运行、验证结果。
   每个测试应针对一个非常特定的行为或配置情况进行测试，这样当测试失败时，可以明确问题所在。
   请尽可能遵循此标准。
1. 将示例功能测试 [ParameterTest](https://github.com/PX4/PX4-Autopilot/blob/main/src/lib/parameters/ParameterTest.cpp) 复制并重命名为被测试代码所在目录中。
1. 将类名从 `ParameterTest` 改为更能代表被测试代码的名称。
1. 将新文件添加到目录的 `CMakeLists.txt` 中。
   它应该类似于 `px4_add_functional_gtest(SRC MyNewFunctionalTest.cpp LINKLIBS <library_to_be_tested>)`
1. 添加所需的测试功能。
   这意味着需要包含特定测试所需的头文件，添加新测试（每个测试使用独立名称），并编写测试初始化逻辑、运行被测试代码以及验证其行为是否符合预期的代码。
1. 如果需要额外的库依赖项，也应按照上述方式在 `LINKLIBS` 后添加到 CMakeLists 中。

测试可以通过 `make tests` 执行，之后你可以在 `build/px4_sitl_test/functional-MyNewFunctional` 中找到生成的二进制文件。
它可以直接在调试器中运行，但请注意每次可执行文件调用时应仅运行一个测试，使用 [--gtest_filter=\<regex\>](https://github.com/google/googletest/blob/main/docs/advanced.md#running-a-subset-of-the-tests) 参数指定测试集，因为部分 uORB 和参数库代码不会完全清理资源，多次初始化可能导致未定义行为。

## 编写 SITL 单元测试

当需要使用所有飞行控制器组件（驱动、时间等）时，应使用 SITL 单元测试。  
这些测试运行速度较慢（每个新模块需要 1 秒以上），且调试难度更大，因此通常仅在必要时使用。

创建新 SITL 单元测试的步骤如下：

1. 参考示例 [Unittest-class](https://github.com/PX4/PX4-Autopilot/blob/main/src/include/unit_test.h)。
1. 在 [tests](https://github.com/PX4/PX4-Autopilot/tree/main/src/systemcmds/tests) 中创建新 .cpp 文件，命名为 **test\_[description].cpp**。
1. 在 **test\_[description].cpp** 中包含基础 unittest-class `<unit_test.h>` 以及实现新功能测试所需的所有文件。
1. 在 **test\_[description].cpp** 中创建继承自 `UnitTest` 的类 `[Description]Test`。
1. 在 `[Description]Test` 类中声明公共方法 `virtual bool run_tests()`。
1. 在 `[Description]Test` 类中声明所有私有方法以测试相关功能（`test1()`、`test2()` 等）。
1. 在 **test\_[description].cpp** 中实现 `run_tests()` 方法，运行每个测试[1,2,...]。
1. 在 **test\_[description].cpp** 中实现各个测试。
1. 在 **test\_[description].cpp** 底部声明测试。

   ```cpp
   ut_declare_test_c(test_[description], [Description]Test)
   ```

   以下是模板：

   ```cpp
   #include <unit_test.h>
   #include "[new feature].h"
   ...

   class [Description]Test : public UnitTest
   {
   public:
       virtual bool run_tests();

   private:
       bool test1();
       bool test2();
       ...
   };

   bool [Description]Test::run_tests()
   {
       ut_run_test(test1)
       ut_run_test(test2)
       ...

       return (_tests_failed == 0);
   }

   bool [Description]Test::test1()
   {
       ut_[name of one of the unit test functions](...
       ut_[name of one of the unit test functions](...
       ...

       return true;
   }

   bool [Description]Test::test2()
   {
       ut_[name of one of the unit test functions](...
       ut_[name of one of the unit test functions](...
       ...

       return true;
   }
   ...

   ut_declare_test_c(test_[description], [Description]Test)
   ```

   注意 `ut_[name of one of the unit test functions]` 对应 [unit_test.h](https://github.com/PX4/PX4-Autopilot/blob/main/src/include/unit_test.h) 中定义的某个单元测试函数。

1. 在 [tests_main.h](https://github.com/PX4/PX4-Autopilot/blob/main/src/systemcmds/tests/tests_main.h) 中定义新测试：

   ```cpp
   extern int test_[description](int argc, char *argv[]);
   ```

1. 在 [tests_main.c](https://github.com/PX4/PX4-Autopilot/blob/main/src/systemcmds/tests/tests_main.c) 中添加描述名称、测试函数和选项：

   ```cpp
   ...
   } tests[] = {
       {...
       {"[description]", test_[description], OPTION},
       ...
   }
   ```

   `OPTION` 可以是 `OPT_NOALLTEST`、`OPT_NOJIGTEST` 或 `0`，这取决于在 px4 shell 中调用以下两个命令之一时的处理方式：

   ```sh
   pxh> tests all
   ```

   或

   ```sh
   pxh> tests jig
   ```

   如果测试的选项为 `OPT_NOALLTEST`，则调用 `tests all` 时会排除该测试。同样，当调用 `test jig` 时，`OPT_NOJITEST` 会排除测试。选项 `0` 表示测试从不被排除，这是大多数开发者希望使用的。

1. 将测试 `test_[description].cpp` 添加到 [CMakeLists.txt](https://github.com/PX4/PX4-Autopilot/blob/main/src/systemcmds/tests/CMakeLists.txt)。

## 本机测试

从 bash 直接运行完整的 GTest 单元测试、GTest 功能测试和 SITL 单元测试列表：

```sh
make tests
```

各个 GTest 测试二进制文件位于 `build/px4_sitl_test/` 目录中，可在大多数 IDE 的调试器中直接运行。

使用 ctest 名称的正则表达式过滤器，通过以下命令运行测试子集：

```sh
make tests TESTFILTER=<regex filter expression>
```

例如：

- `make tests TESTFILTER=unit` 仅运行 GTest 单元测试
- `make tests TESTFILTER=sitl` 仅运行仿真测试
- `make tests TESTFILTER=Attitude` 仅运行 `AttitudeControl` 测试