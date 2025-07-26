# MAVROS _Offboard_ 控制示例 (Python)

本教程通过 [Gazebo Classic](../sim_gazebo_classic/index.md) 中模拟的 Iris 四旋翼飞行器，展示了使用 MAVROS Python 的 _OFFBOARD_ 控制基础。它提供了逐步说明，演示如何开始开发控制机体的程序并在模拟中运行代码。

在本教程结束时，您应能看到与下文视频中相同的行为，即以缓慢的方式上升到2米高度。

:::warning
_OFFBOARD_ 控制存在危险。
如果在真实机体上操作，请确保在出现问题时能够恢复手动控制。
:::

:::tip
此示例使用 Python。
其他 Python 示例可在此处找到：[integrationtests/python_src/px4_it/mavros](https://github.com/PX4/PX4-Autopilot/tree/main/integrationtests/python_src/px4_it/mavros)。
:::

<a id="offb_video"></a>

<video width="100%" autoplay="true" controls="true">
 <source src="../../assets/simulation/gazebo_classic/gazebo_offboard.webm" type="video/webm">
</video>

## 创建ROS包

1. 打开终端并进入 `~/catkin_ws/src` 目录

   ```sh
   roscd  # 应进入 ~/catkin_ws/devel 目录
   cd ..
   cd src
   ```

2. 在 `~/catkin_ws/src` 目录中创建一个名为 `offboard_py` 的新包（此处为例），并指定依赖 `rospy`：

   ```sh
   catkin_create_pkg offboard_py rospy
   ```

3. 在 `~/catkin_ws/` 目录中构建新包：

   ```sh
   cd .. # 假设上一目录为 ~/catkin_ws/src
   catkin build
   source devel/setup.bash
   ```

4. 现在可通过以下命令进入该包目录：

   ```sh
   roscd offboard_py
   ```

5. 为存储Python文件，在包中创建一个名为 `/scripts` 的新文件夹：

   ```sh
   mkdir scripts
   cd scripts
   ```

## 代码

创建 ROS 包和脚本文件夹后，您可以开始编写 Python 脚本。  
在脚本文件夹中创建 `offb_node.py` 文件并赋予其可执行权限：

```sh
touch offb_node.py
chmod +x offb_node.py
```

之后，打开 `offb_node.py` 文件并粘贴以下代码：

```py
"""
 * 文件: offb_node.py
 * 在 Gazebo Classic 9 SITL 环境中构建并测试
"""

#! /usr/bin/env python

import rospy
from geometry_msgs.msg import PoseStamped
from mavros_msgs.msg import State
from mavros_msgs.srv import CommandBool, CommandBoolRequest, SetMode, SetModeRequest

current_state = State()

def state_cb(msg):
    global current_state
    current_state = msg


if __name__ == "__main__":
    rospy.init_node("offb_node_py")

    state_sub = rospy.Subscriber("mavros/state", State, callback = state_cb)

    local_pos_pub = rospy.Publisher("mavros/setpoint_position/local", PoseStamped, queue_size=10)

    rospy.wait_for_service("/mavros/cmd/arming")
    arming_client = rospy.ServiceProxy("mavros/cmd/arming", CommandBool)

    rospy.wait_for_service("/mavros/set_mode")
    set_mode_client = rospy.ServiceProxy("mavros/set_mode", SetMode)


    # 设置点发布的频率必须高于 2Hz
    rate = rospy.Rate(20)

    # 等待飞控连接
    while(not rospy.is_shutdown() and not current_state.connected):
        rate.sleep()

    pose = PoseStamped()

    pose.pose.position.x = 0
    pose.pose.position.y = 0
    pose.pose.position.z = 2

    # 在开始前发送若干个位置设置点
    for i in range(100):
        if(rospy.is_shutdown()):
            break

        local_pos_pub.publish(pose)
        rate.sleep()

    offb_set_mode = SetModeRequest()
    offb_set_mode.custom_mode = 'OFFBOARD'

    arm_cmd = CommandBoolRequest()
    arm_cmd.value = True

    last_req = rospy.Time.now()

    while(not rospy.is_shutdown()):
        if(current_state.mode != "OFFBOARD" and (rospy.Time.now() - last_req) > rospy.Duration(5.0)):
            if(set_mode_client.call(offb_set_mode).mode_sent == True):
                rospy.loginfo("OFFBOARD 模式已启用")

            last_req = rospy.Time.now()
        else:
            if(not current_state.armed and (rospy.Time.now() - last_req) > rospy.Duration(5.0)):
                if(arming_client.call(arm_cmd).success == True):
                    rospy.loginfo("机体已武装")

                last_req = rospy.Time.now()

        local_pos_pub.publish(pose)

        rate.sleep()

```

## 代码解释

`mavros_msgs` 包包含了操作 MAVROS 包提供服务和话题所需的所有自定义消息。  
所有服务和话题及其对应的消息类型均在 [mavros wiki](http://wiki.ros.org/mavros) 中有详细说明。

```py
import rospy
from geometry_msgs.msg import PoseStamped
from mavros_msgs.msg import State
from mavros_msgs.srv import CommandBool, CommandBoolRequest, SetMode, SetModeRequest
```

我们创建一个简单的回调函数用于保存自动驾驶仪的当前状态。  
这将允许我们检查连接状态、解锁状态和 _OFFBOARD_ 标志：

```py
current_state = State()

def state_cb(msg):
    global current_state
    current_state = msg
```

我们实例化一个发布器用于发布指令本地位置，同时创建适当的客户端用于请求解锁和模式切换。  
请注意，对于您自己的系统，"mavros" 前缀可能不同，这取决于在启动文件中给节点命名的名称。

```py
state_sub = rospy.Subscriber("mavros/state", State, callback = state_cb)

local_pos_pub = rospy.Publisher("mavros/setpoint_position/local", PoseStamped, queue_size=10)

rospy.wait_for_service("/mavros/cmd/arming")
arming_client = rospy.ServiceProxy("mavros/cmd/arming", CommandBool)

rospy.wait_for_service("/mavros/set_mode")
set_mode_client = rospy.ServiceProxy("mavros/set_mode", SetMode)
```

PX4 在两次 _OFFBOARD_ 指令之间有 500ms 的超时限制。  
如果超过该超时时间，指挥器将回退到机体进入 _OFFBOARD_ 模式前的最后一个模式。  
这就是为什么**发布频率必须高于 2 Hz** 的原因，同时还要考虑可能的延迟。  
这也是**建议从 _Position_ 模式进入 _OFFBOARD_ 模式**的原因，这样当机体脱离 _OFFBOARD_ 模式时，它会立即停止并悬停。

此处我们设置适当发布频率：

```py
# 设定点发布频率必须高于2Hz
rate = rospy.Rate(20)
```

在发布任何内容之前，我们需要等待MAVROS和自动驾驶仪之间的连接建立。
此循环应在接收到心跳消息后立即退出。

```py
# 等待飞控连接
while(not rospy.is_shutdown() and not current_state.connected):  
    rate.sleep()
```

虽然PX4在航空航天NED坐标系下运行，但MAVROS会将这些坐标转换为标准ENU坐标系，反之亦然。  
这就是为什么我们设置`z`为正2的原因：

```py
pose = PoseStamped()

pose.pose.position.x = 0
pose.pose.position.y = 0
pose.pose.position.z = 2
```

在进入_OFFBOARD_模式之前，必须已经启动了设定点的传输。  
否则模式切换将被拒绝。  
以下是任意选择的`100`值：

```py

# 在启动前发送一些设定点  
for i in range(100):  
    if(rospy.is_shutdown()):  
        break  

    local_pos_pub.publish(pose)  
    rate.sleep()  
```
我们准备一条消息请求，将自定义模式设置为`OFFBOARD`。  
[支持的模式列表](http://wiki.ros.org/mavros/CustomModes#PX4_native_flight_stack)可供参考。  

```py  
offb_set_mode = SetModeRequest()  
offb_set_mode.custom_mode = 'OFFBOARD'  
```  

其余代码基本可自行解释。我们尝试切换到_Offboard_模式，随后武装四轴飞行器以允许其飞行。我们通过5秒间隔调用服务，避免向自动驾驶仪发送过多请求。在相同循环中，我们继续以先前定义的频率发送请求的位姿。  

```py  
arm_cmd = CommandBoolRequest()  
arm_cmd.value = True  

last_req = rospy.Time.now()  

while(not rospy.is_shutdown()):  
    if(current_state.mode != "OFFBOARD" and (rospy.Time.now() - last_req) > rospy.Duration(5.0)):  
        if(set_mode_client.call(offb_set_mode).mode_sent == True):  
            rospy.loginfo("OFFBOARD enabled")  

        last_req = rospy.Time.now()  
    else:  
        if(not current_state.armed and (rospy.Time.now() - last_req) > rospy.Duration(5.0)):  
            if(arming_client.call(arm_cmd).success == True):  
                rospy.loginfo("Vehicle armed")  

            last_req = rospy.Time.now()  

    local_pos_pub.publish(pose)  

    rate.sleep()  
```  

:::tip  
此代码为简化版，仅用于说明。在更复杂的系统中，通常有用新线程周期性发布设定点。  
:::

## 创建 ROS 启动文件

在您的 `offboard_py` 包中，于 `~/catkin_ws/src/offboard_py/src` 目录下创建一个名为 `launch` 的新文件夹。  
这是存放该包启动文件的目录。  
之后创建第一个启动文件，在本例中我们将其命名为 `start_offb.launch`。

```sh
roscd offboard_py
mkdir launch
cd launch
touch start_offb.launch
```

对于 `start_offb.launch` 文件，请复制以下代码：

```xml
<?xml version="1.0"?>
<launch>
	<!-- Include the MAVROS node with SITL and Gazebo -->
	<include file="$(find px4)/launch/mavros_posix_sitl.launch">
	</include>

	<!-- Our node to control the drone -->
	<node pkg="offboard_py" type="offb_node.py" name="offb_node_py" required="true" output="screen" />
</launch>
```

如您所见，已包含 `mavros_posix_sitl.launch` 文件。  
该文件负责启动 MAVROS、PX4 SITL、Gazebo Classic 环境，并在指定世界中生成机体（更多信息请参见[此处](https://github.com/PX4/PX4-Autopilot/blob/main/launch/mavros_posix_sitl.launch)）。

:::tip
`mavros_posix_sitl.launch` 文件包含多个可自定义的参数，例如生成的机体类型或 Gazebo Classic 世界（完整列表请参见[此处](https://github.com/PX4/PX4-Autopilot/blob/main/launch/mavros_posix_sitl.launch)）。  

您可以通过在 _include_ 标签内声明参数来覆盖 `mavros_posix_sitl.launch` 中定义的默认值。  
例如，若需在 `warehouse.world` 中生成机体，可以这样写：

```xml
<!-- Include the MAVROS node with SITL and Gazebo -->
<include file="$(find px4)/launch/mavros_posix_sitl.launch">
    <arg name="world" default="$(find mavlink_sitl_gazebo)/worlds/warehouse.world"/>
</include>
```

:::

## 启动你的脚本

如果所有步骤已完成，你现在应该能够启动并测试你的脚本。

在终端中输入：

```sh
roslaunch offboard_py start_offb.launch
```

你应该会看到PX4固件开始运行，同时Gazebo Classic应用程序也启动。  
当_OFFBOARD_模式设置成功且机体已武装后，会观察到[视频](#offb_video)中展示的行为。

:::warning
运行脚本时可能会出现如下错误提示：

> Resource not found: px4  
> ROS path [0] = ...  
> ...

这意味着PX4 SITL没有被加入到路径中。  
为解决此问题，请在`.bashrc`文件末尾添加以下内容：

```sh
source ~/PX4-Autopilot/Tools/simulation/gazebo/setup_gazebo.bash ~/PX4-Autopilot ~/PX4-Autopilot/build/px4_sitl_default
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4-Autopilot
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4-Autopilot/Tools/simulation/gazebo-classic/sitl_gazebo-classic
export GAZEBO_PLUGIN_PATH=$GAZEBO_PLUGIN_PATH:/usr/lib/x86_64-linux-gnu/gazebo-9/plugins
```

现在在终端中进入家目录，运行以下命令以应用上述更改：

```sh
source .bashrc
```

完成此步骤后，每次打开新终端窗口时无需再担心此错误。  
如果错误再次出现，只需执行`source .bashrc`即可修复。  
此解决方案来自这个[issue](https://github.com/mzahana/px4_fast_planner/issues/4)讨论线程，你可以在其中获取该问题的更多信息。
:::