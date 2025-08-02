# 从MAVROS向PX4发送自定义消息

:::warning
本文已针对以下环境进行了测试：
- **Ubuntu:** 20.04
- **ROS:** Noetic
- **PX4固件:** v1.13

不过这些步骤相对通用，因此在其他发行版/版本上只需进行少量或无需修改即可正常工作。
:::

<!-- 内容经 @JoonmoAhn 在 https://github.com/JoonmoAhn/Sending-Custom-Message-from-MAVROS-to-PX4/issues/1 中的许可授权转载 -->

## MAVROS 安装

按照 [mavlink/mavros](https://github.com/mavlink/mavros/blob/master/mavros/index.md) 中的 *Source Installation* 指南安装 "ROS Kinetic"。

## MAVROS

1. 首先创建一个新的MAVROS插件，本例中命名为 **keyboard_command.cpp** (位于 **workspace/src/mavros/mavros_extras/src/plugins**)，使用以下代码：

   该代码订阅ROS主题 `/mavros/keyboard_command/keyboard_sub` 中的 'char' 消息，并将其作为MAVLink消息发送。
   ```c
    #include <mavros/mavros_plugin.h>
    #include <pluginlib/class_list_macros.h>
    #include <iostream>
    #include <std_msgs/Char.h>

    namespace mavros {
    namespace extra_plugins{

    class KeyboardCommandPlugin : public plugin::PluginBase {
    public:
        KeyboardCommandPlugin() : PluginBase(),
            nh("~keyboard_command")

       { };

        void initialize(UAS &uas_)
        {
            PluginBase::initialize(uas_);
            keyboard_sub = nh.subscribe("keyboard_sub", 10, &KeyboardCommandPlugin::keyboard_cb, this);
        };

        Subscriptions get_subscriptions()
        {
            return {/* RX disabled */ };
        }

    private:
        ros::NodeHandle nh;
        ros::Subscriber keyboard_sub;

       void keyboard_cb(const std_msgs::Char::ConstPtr &req)
        {
            std::cout << "Got Char : " << req->data <<  std::endl;
            mavlink::common::msg::KEY_COMMAND kc {};
            kc.command = req->data;
            UAS_FCU(m_uas)->send_message_ignore_drop(kc);
        }
    };
    }   // namespace extra_plugins
    }   // namespace mavros

   PLUGINLIB_EXPORT_CLASS(mavros::extra_plugins::KeyboardCommandPlugin, mavros::plugin::PluginBase)
   ```

1. 编辑 **mavros_plugins.xml** (位于 **workspace/src/mavros/mavros_extras**) 并添加以下内容：
   ```xml
   <class name="keyboard_command" type="mavros::extra_plugins::KeyboardCommandPlugin" base_class_type="mavros::plugin::PluginBase">
        <description>接受键盘命令。</description>
   </class>
   ```

1. 编辑 **CMakeLists.txt** (位于 **workspace/src/mavros/mavros_extras**) 并在 `add_library` 中添加以下行：
   ```cmake
   add_library( 
   ...
     src/plugins/keyboard_command.cpp 
   )
   ```

1. 在 **common.xml** 中 (**workspace/src/mavlink/message_definitions/v1.0**)，复制以下内容添加你的MAVLink消息：
   ```xml
   ...
     <message id="229" name="KEY_COMMAND">
        <description>键盘字符命令。</description>
        <field type="char" name="command"> </field>
      </message>
   ...
   ```

## PX4 修改

1. 在 **common.xml** (位于 **PX4-Autopilot/src/modules/mavlink/mavlink/message_definitions/v1.0**) 中，按以下方式添加你的 MAVLink 消息（与上文 MAVROS 部分的流程相同）：
   ```xml
   ...
     <message id="229" name="KEY_COMMAND">
        <description>键盘字符命令。</description>
        <field type="char" name="command"> </field>
      </message>
   ...
   ```

   :::warning
   确保以下目录中的 **common.xml** 文件完全一致：
   - `PX4-Autopilot/src/modules/mavlink/mavlink/message_definitions/v1.0`
   - `workspace/src/mavlink/message_definitions/v1.0`
   完全一致。
   :::

1. 在 (PX4-Autopilot/msg) 中创建你自己的 uORB 消息文件 **key_command.msg**。对于此示例，"key_command.msg" 仅包含以下代码：
   ```
   uint64 timestamp # 系统启动后的时间（微秒）
   char cmd
   ```

   然后，在 **CMakeLists.txt** (位于 **PX4-Autopilot/msg**) 中包含以下内容：

   ```cmake
   set(
   ...
        key_command.msg
        )
   ```

1. 编辑 **mavlink_receiver.h** (位于 **PX4-Autopilot/src/modules/mavlink**)

   ```cpp
   ...
   #include <uORB/topics/key_command.h>
   ...
   class MavlinkReceiver
   {
   ...
   private:
       void handle_message_key_command(mavlink_message_t *msg);
   ...
       orb_advert_t _key_command_pub{nullptr};
   }
   ```

1. 编辑 **mavlink_receiver.cpp** (位于 **PX4-Autopilot/src/modules/mavlink**)。
   这里是 PX4 接收来自 ROS 发送的 MAVLink 消息并将其作为 uORB 主题发布的部分。
   ```cpp
   ...
   void MavlinkReceiver::handle_message(mavlink_message_t *msg)
   {
   ...
    case MAVLINK_MSG_ID_KEY_COMMAND:
           handle_message_key_command(msg);
           break;
   ...
   }
   ...
   void
   MavlinkReceiver::handle_message_key_command(mavlink_message_t *msg)
   {
       mavlink_key_command_t man;
       mavlink_msg_key_command_decode(msg, &man);

   struct key_command_s key = {};

       key.timestamp = hrt_absolute_time();
       key.cmd = man.command;

       if (_key_command_pub == nullptr) {
           _key_command_pub = orb_advertise(ORB_ID(key_command), &key);

       } else {
           orb_publish(ORB_ID(key_command), _key_command_pub, &key);
       }
   }
   ```

1. 创建你自己的 uORB 主题订阅器，方式与任何示例订阅器模块相同。
   例如，我们在此创建模型在 (/PX4-Autopilot/src/modules/key_receiver)。
   在此目录中，创建三个文件 **CMakeLists.txt**，**key_receiver.cpp**，**Kconfig**
   每个文件内容如下：

   -CMakeLists.txt

   ```cmake
   px4_add_module(
       MODULE modules__key_receiver
       MAIN key_receiver
       STACK_MAIN 2500
       STACK_MAX 4000
       SRCS
           key_receiver.cpp
       DEPENDS

       )
   ```

   -key_receiver.cpp

   ```
   #include <px4_platform_common/px4_config.h>
   #include <px4_platform_common/tasks.h>
   #include <px4_platform_common/posix.h>
   #include <unistd.h>
   #include <stdio.h>
   #include <poll.h>
   #include <string.h>
   #include <math.h>

   #include <uORB/uORB.h>
   #include <uORB/topics/key_command.h>

   extern "C" __EXPORT int key_receiver_main(int argc, char **argv);

   int key_receiver_main(int argc, char **argv)
   {
       int key_sub_fd = orb_subscribe(ORB_ID(key_command));
       orb_set_interval(key_sub_fd, 200); // 将更新速率限制为 200ms

       px4_pollfd_struct_t fds[] = {
           { .fd = key_sub_fd,   .events = POLLIN },
       };

       int error_counter = 0;

	   for (int i = 0; i < 10; i++)
       {
           int poll_ret = px4_poll(fds, 1, 1000);

           if (poll_ret == 0)
           {
               PX4_ERR("一秒内未收到数据");
           }

           else if (poll_ret < 0)
           {
               if (error_counter < 10 || error_counter % 50 == 0)
               {
                   PX4_ERR("poll() 返回错误值: %d", poll_ret);
               }

               error_counter++;
           }

           else
           {
               if (fds[0].revents & POLLIN)
               {
                   struct key_command_s input;
                   orb_copy(ORB_ID(key_command), key_sub_fd, &input);
                   PX4_INFO("接收到的字符 : %c", input.cmd);
                }
           }
       }
       return 0;
   }
   ```

   -Kconfig

   ```
    menuconfig MODULES_KEY_RECEIVER
	bool "key_receiver"
	default n
	---help---
		启用 key_receiver 支持

   ```

   更详细的解释请参见主题 [编写你的第一个应用程序](../modules/hello_sky.md)。

1. 最后，在 **PX4-Autopilot/boards/** 中对应你使用的板卡的 **default.px4board** 文件中添加你的模块。
   例如：
   - 对于 Pixhawk 4，在 **PX4-Autopilot/boards/px4/fmu-v5/default.px4board** 中添加以下代码：
   - 对于 SITL，在 **PX4-Autopilot/boards/px4/sitl/default.px4board** 中添加以下代码：

   ```
    CONFIG_MODULES_KEY_RECEIVER=y
   ```

现在你就可以构建所有工作内容了！

## 构建

### 为 ROS 构建

1. 在您的工作空间中输入：`catkin build`。
1. 之前，您需要设置您的 "px4.launch" 文件（位于 `/workspace/src/mavros/mavros/launch` 目录中）。  
   按如下方式编辑 "px4.launch"。
   如果您使用 USB 连接计算机与 Pixhawk，需要将 "fcu_url" 设置为如下所示。
   如果您使用 CP2102 连接计算机与 Pixhawk，需要将 "ttyACM0" 替换为 "ttyUSB0"。
   如果您使用 SITL 连接到终端，需要将 "/dev/ttyACM0:57600" 替换为 "udp://:14540@127.0.0.1:14557"。
   修改 "gcs_url" 是为了通过 UDP 连接 Pixhawk，因为串口通信无法同时接受 MAVROS 和您的 nutshell 连接。

1. 在 "xxx.xx.xxx.xxx" 处填写您的 IP 地址
   ```xml
   ...
     <arg name="fcu_url" default="/dev/ttyACM0:57600" />
     <arg name="gcs_url" default="udp://:14550@xxx.xx.xxx.xxx:14557" />
   ...
   ```

### 为 PX4 构建

1. 清理之前构建的 PX4-Autopilot 目录。在 **PX4-Autopilot** 根目录执行：
    ```sh
    make clean
    ```

1. 按正常方式构建 PX4-Autopilot 并上传 [in the normal way](../dev_setup/building_px4.md#nuttx-pixhawk-based-boards)。 

    例如：
    
    - 为 Pixhawk 4/FMUv5 构建时，在 PX4-Autopilot 根目录执行以下命令：
    ```sh
    make px4_fmu-v5_default upload
    ```
    - 为 SITL 构建时，在 PX4-Autopilot 根目录执行以下命令（使用 jmavsim 模拟）：
    ```sh
    make px4_sitl jmavsim
    ```

## 运行代码

下一步测试MAVROS消息是否已发送到PX4。

### 运行ROS

1. 在终端中输入
   ```sh
   roslaunch mavros px4.launch
   ```
1. 在第二个终端运行：
   ```sh
   rostopic pub -r 10 /mavros/keyboard_command/keyboard_sub std_msgs/Char 97
   ```
   这表示以10Hz频率持续发布97（ASCII中的'a'）到ROS主题"/mavros/keyboard_command/keyboard_sub"，消息类型为"std_msgs/Char"。

### 运行PX4

1. 通过UDP进入Pixhawk的nutshell环境。
   将xxx.xx.xxx.xxx替换为你的IP地址。
   ```sh
   cd PX4-Autopilot/Tools
   ./mavlink_shell.py xxx.xx.xxx.xxx:14557 --baudrate 57600
   ```

1. 几秒后，按几次**Enter**键。
   终端中应出现如下提示符：
   ```sh
   nsh>
   nsh>
   ```
   输入"key_receiver"运行你的订阅模块。
   ```
   nsh> key_receiver
   ```

检查是否成功从ROS主题接收到`a`。