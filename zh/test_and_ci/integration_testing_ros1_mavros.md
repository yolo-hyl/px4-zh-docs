# 使用 ROS 1 进行集成测试

本主题介绍如何运行（并扩展）PX4 的 ROS（1）和 MAVROS 集成测试。

:::warning 该测试框架已弃用。
仅在新测试用例需要ROS 1时才应使用。

[MAVSDK集成测试](../test_and_ci/integration_testing_mavsdk.md) 是编写新测试的首选方式。
:::

:::note
所有 PX4 集成测试均由我们的 [持续集成](../test_and_ci/continous_integration.md) 系统自动执行。
:::

## 前提条件

- [Gazebo Classic Simulator](../sim_gazebo_classic/index.md)
- [ROS和MAVROS](../simulation/ros_interface.md)

## 执行测试

运行 MAVROS 测试的步骤：

```sh
source <catkin_ws>/devel/setup.bash
cd <PX4-Autopilot_clone>
make px4_sitl_default sitl_gazebo
make <test_target>
```

`test_target` 是 makefile 中的目标，取自以下集合：_tests_mission_、_tests_mission_coverage_、_tests_offboard_ 和 _tests_avoidance_。

也可以通过运行 `test/` 目录下的测试脚本来直接执行测试：

```sh
source <catkin_ws>/devel/setup.bash
cd <PX4-Autopilot_clone>
make px4_sitl_default sitl_gazebo
./test/<test_bash_script> <test_launch_file>
```

例如：

```sh
./test/rostest_px4_run.sh mavros_posix_tests_offboard_posctl.test
```

也可以通过启用 GUI 来查看测试过程（默认情况下测试以无头模式运行）：

```sh
./test/rostest_px4_run.sh mavros_posix_tests_offboard_posctl.test gui:=true headless:=false
```

**.test** 文件会启动 `integrationtests/python_src/px4_it/mavros/` 中定义的对应 Python 测试。

## 编写新的 MAVROS 测试（Python）

本节说明如何使用 ROS 1/MAVROS 编写新的 Python 测试，进行测试，并将其添加到 PX4 测试套件中。

建议参考现有测试作为示例/灵感（[integrationtests/python_src/px4_it/mavros/](https://github.com/PX4/PX4-Autopilot/tree/main/integrationtests/python_src/px4_it/mavros)）。
官方 ROS 文档也包含关于如何使用 [unittest](http://wiki.ros.org/unittest) 的信息（本测试套件基于此）。

编写新测试的步骤：

1. 通过复制下方的空测试框架创建一个新的测试脚本：

   ```python
   #!/usr/bin/env python
   # [... LICENSE ...]

   #
   # @author Example Author <author@example.com>
   #
   PKG = 'px4'

   import unittest
   import rospy
   import rosbag

   from sensor_msgs.msg import NavSatFix

   class MavrosNewTest(unittest.TestCase):
   	"""
   	Test description
   	"""

   	def setUp(self):
   		rospy.init_node('test_node', anonymous=True)
   		rospy.wait_for_service('mavros/cmd/arming', 30)

   		rospy.Subscriber("mavros/global_position/global", NavSatFix, self.global_position_callback)
   		self.rate = rospy.Rate(10) # 10hz
   		self.has_global_pos = False

   	def tearDown(self):
   		pass

   	#
   	# General callback functions used in tests
   	#
   	def global_position_callback(self, data):
   		self.has_global_pos = True

   	def test_method(self):
   		"""Test method description"""

   		# FIXME: hack to wait for simulation to be ready
   		while not self.has_global_pos:
   			self.rate.sleep()

   		# TODO: execute test

   if __name__ == '__main__':
   	import rostest
   	rostest.rosrun(PKG, 'mavros_new_test', MavrosNewTest)
   ```

1. 仅运行新测试

    - 启动模拟器：

      ```sh
      cd <PX4-Autopilot_clone>
      source Tools/simulation/gazebo/setup_gazebo.bash
      roslaunch launch/mavros_posix_sitl.launch
      ```

    - 运行测试（在新终端中）：

      ```sh
      cd <PX4-Autopilot_clone>
      source Tools/simulation/gazebo/setup_gazebo.bash
      rosrun px4 mavros_new_test.py
      ```

1. 将新测试节点添加到启动文件中

    - 在 `test/` 中创建新的 `<test_name>.test` ROS 启动文件。
    - 使用基础脚本 _rostest_px4_run.sh_ 或 _rostest_avoidance_run.sh_ 调用测试文件。

1. （可选）在 Makefile 中创建新目标

    - 打开 Makefile
    - 搜索 _Testing_ 部分
    - 添加新目标名称并调用测试

   例如：

   ```sh
   tests_<new_test_target_name>: rostest
   	@"$(SRC_DIR)"/test/rostest_px4_run.sh mavros_posix_tests_<new_test>.test
   ```

按照上述说明运行测试。