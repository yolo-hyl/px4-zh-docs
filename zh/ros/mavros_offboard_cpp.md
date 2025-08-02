# MAVROS _Offboard_ 控制示例 (C++)

本教程通过 Gazebo Classic/SITL 仿真中的 Iris 四旋翼无人机，展示了 MAVROS 的 _Offboard_ 控制基础操作。  
教程结束时，你应该能看到与下方视频相同的行为，即缓慢上升至 2 米高度。

:::warning  
_Offboard_ 控制具有危险性。  
如果在真实机体上操作，务必确保在出现问题时能够重新获得手动控制。  
:::

:::tip  
本示例使用 C++。  
Python 的类似示例可参考 [ROS/MAVROS Offboard 示例 (Python)](../ros/mavros_offboard_python.md)（另见 [integrationtests/python_src/px4_it/mavros](https://github.com/PX4/PX4-Autopilot/tree/main/integrationtests/python_src/px4_it/mavros) 中的示例）。  
:::

<video width="100%" autoplay="true" controls="true">  
  <source src="../../assets/simulation/gazebo_classic/gazebo_offboard.webm" type="video/webm">  
</video>

## 代码

在您的ROS包中创建 `offb_node.cpp` 文件（同时将其添加到 `CMakeList.txt` 以进行编译），并将以下内容粘贴到其中：

```cpp
/**
 * @file offb_node.cpp
 * @brief 离板控制示例节点，使用MAVROS版本0.19.x、PX4 Pro飞行栈，并在Gazebo Classic SITL中测试
 */

#include <ros/ros.h>
#include <geometry_msgs/PoseStamped.h>
#include <mavros_msgs/CommandBool.h>
#include <mavros_msgs/SetMode.h>
#include <mavros_msgs/State.h>

mavros_msgs::State current_state;
void state_cb(const mavros_msgs::State::ConstPtr& msg){
    current_state = *msg;
}

int main(int argc, char **argv)
{
    ros::init(argc, argv, "offb_node");
    ros::NodeHandle nh;

    ros::Subscriber state_sub = nh.subscribe<mavros_msgs::State>
            ("mavros/state", 10, state_cb);
    ros::Publisher local_pos_pub = nh.advertise<geometry_msgs::PoseStamped>
            ("mavros/setpoint_position/local", 10);
    ros::ServiceClient arming_client = nh.serviceClient<mavros_msgs::CommandBool>
            ("mavros/cmd/arming");
    ros::ServiceClient set_mode_client = nh.serviceClient<mavros_msgs::SetMode>
            ("mavros/set_mode");

    // 设定点发布频率必须大于2Hz
    ros::Rate rate(20.0);

    // 等待FCU连接
    while(ros::ok() && !current_state.connected){
        ros::spinOnce();
        rate.sleep();
    }

    geometry_msgs::PoseStamped pose;
    pose.pose.position.x = 0;
    pose.pose.position.y = 0;
    pose.pose.position.z = 2;

    // 在开始前发送几个设定点
    for(int i = 100; ros::ok() && i > 0; --i){
        local_pos_pub.publish(pose);
        ros::spinOnce();
        rate.sleep();
    }

    mavros_msgs::SetMode offb_set_mode;
    offb_set_mode.request.custom_mode = "OFFBOARD";

    mavros_msgs::CommandBool arm_cmd;
    arm_cmd.request.value = true;

    ros::Time last_request = ros::Time::now();

    while(ros::ok()){
        if( current_state.mode != "OFFBOARD" &&
            (ros::Time::now() - last_request > ros::Duration(5.0))){
            if( set_mode_client.call(offb_set_mode) &&
                offb_set_mode.response.mode_sent){
                ROS_INFO("离板模式已启用");
            }
            last_request = ros::Time::now();
        } else {
            if( !current_state.armed &&
                (ros::Time::now() - last_request > ros::Duration(5.0))){
                if( arming_client.call(arm_cmd) &&
                    arm_cmd.response.success){
                    ROS_INFO("机体已武装");
                }
                last_request = ros::Time::now();
            }
        }

        local_pos_pub.publish(pose);

        ros::spinOnce();
        rate.sleep();
    }

    return 0;
}
```

## 代码解释

```cpp
#include <ros/ros.h>
#include <geometry_msgs/PoseStamped.h>
#include <mavros_msgs/CommandBool.h>
#include <mavros_msgs/SetMode.h>
#include <mavros_msgs/State.h>
```

`mavros_msgs` 包含MAVROS包提供的服务和主题所需的所有自定义消息。
所有服务和主题及其对应的消息类型都在 [mavros wiki](http://wiki.ros.org/mavros) 中有详细文档。

```cpp
mavros_msgs::State current_state;
void state_cb(const mavros_msgs::State::ConstPtr& msg){
    current_state = *msg;
}
```

我们创建一个简单的回调函数，用于保存自动驾驶仪的当前状态。
这将允许我们检查连接状态、上锁状态和 _Offboard_ 标志。

```cpp
ros::Subscriber state_sub = nh.subscribe<mavros_msgs::State>("mavros/state", 10, state_cb);
ros::Publisher local_pos_pub = nh.advertise<geometry_msgs::PoseStamped>("mavros/setpoint_position/local", 10);
ros::ServiceClient arming_client = nh.serviceClient<mavros_msgs::CommandBool>("mavros/cmd/arming");
ros::ServiceClient set_mode_client = nh.serviceClient<mavros_msgs::SetMode>("mavros/set_mode");
```

我们实例化一个发布器用于发布本地位置指令，并创建相应的客户端用于请求上锁和模式切换。
请注意，对于您自己的系统，"mavros" 前缀可能不同，这取决于在启动文件中为该节点指定的名称。

```cpp
//the setpoint publishing rate MUST be faster than 2Hz
ros::Rate rate(20.0);
```

PX4 在两次 _Offboard_ 命令之间有 500ms 的超时。
如果超过这个超时时间，指挥官将回落到进入 _Offboard_ 模式前的最后一个模式。
这就是为什么发布频率 **必须** 高于 2 Hz，以考虑可能的延迟。
这也是为什么建议从 _Position_ 模式进入 _Offboard_ 模式的原因，这样如果机体退出 _Offboard_ 模式，它会立即停止并悬停。

```cpp
// wait for FCU connection
while(ros::ok() && !current_state.connected){
    ros::spinOnce();
    rate.sleep();
}
```

在发布任何内容之前，我们等待MAVROS和自动驾驶仪之间的连接建立。
该循环会在接收到心跳消息后立即退出。

```cpp
geometry_msgs::PoseStamped pose;
pose.pose.position.x = 0;
pose.pose.position.y = 0;
pose.pose.position.z = 2;
```

尽管 PX4 Pro 飞行堆栈使用航空NED坐标系，但 MAVROS 会将这些坐标转换为标准ENU坐标系，反之亦然。
这就是为什么我们将 `z` 设置为正2的原因。

```cpp
//send a few setpoints before starting
for(int i = 100; ros::ok() && i > 0; --i){
  local_pos_pub.publish(pose);
  ros::spinOnce();
  rate.sleep();
}
```

在进入 _Offboard_ 模式之前，必须已经开始发送设定点。
否则模式切换将被拒绝。此处选择 `100` 是一个任意的数值。

```cpp
mavros_msgs::SetMode offb_set_mode;
offb_set_mode.request.custom_mode = "OFFBOARD";
```

我们将自定义模式设置为 `OFFBOARD`。
支持的模式列表请参考 [支持的模式](http://wiki.ros.org/mavros/CustomModes#PX4_native_flight_stack)。

```cpp
mavros_msgs::CommandBool arm_cmd;
arm_cmd.request.value = true;

ros::Time last_request = ros::Time::now();

while(ros::ok()){
  if( current_state.mode != "OFFBOARD" &&
    (ros::Time::now() - last_request > ros::Duration(5.0))){
    if( set_mode_client.call(offb_set_mode) && offb_set_mode.response.mode_sent) {
      ROS_INFO("Offboard enabled");
    }
    last_request = ros::Time::now();
  } else {
    if( !current_state.armed && (ros::Time::now() - last_request > ros::Duration(5.0))) {
      if( arming_client.call(arm_cmd) && arm_cmd.response.success) {
        ROS_INFO("Vehicle armed");
      }
      last_request = ros::Time::now();
    }
  }

  local_pos_pub.publish(pose);

  ros::spinOnce();
  rate.sleep();
}
```

其余代码相当直观。
我们尝试切换到 _Offboard_ 模式，之后上锁机体以允许其飞行。
我们通过5秒的间隔来调用服务，以避免向自动驾驶仪发送过多请求。
在同一个循环中，我们继续以适当频率发送请求的姿态。

:::tip
此代码为说明目的简化至最低限度。
在大型系统中，通常有用创建一个新线程来定期发布设定点。
:::