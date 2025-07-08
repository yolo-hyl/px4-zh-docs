# 流式传输 MAVLink 消息

本教程演示如何将uORB消息作为MAVLink消息进行流式传输，适用于标准消息和自定义消息。

## 概述

[MAVLink消息](../middleware/mavlink.md)通过继承自`MavlinkStream`的流类进行传输，该类已被添加到PX4的流列表中。  
该类包含你需要实现的框架方法，以便PX4能够从生成的MAVLink消息定义中获取所需的信息。  
它还有一个`send()`方法，每次需要发送消息时都会调用该方法——你需要重写此方法，将信息从uORB订阅复制到待发送的MAVLink消息对象中。

创建流类后，相应的消息可以按需传输。  
你还可以根据MAVLink配置，将PX4配置为默认传输该消息。

## 预备条件

通常你已经拥有一个[uORB](../middleware/uorb.md)消息，其中包含你想传输的信息，并定义了一个MAVLink消息用于传输。

在本示例中，我们假设你想将已存在的[BatteryStatus](../msg_docs/BatteryStatus.md) uORB消息传输到一个名为`BATTERY_STATUS_DEMO`的新MAVLink电池状态消息中。

将此`BATTERY_STATUS_DEMO`消息复制到PX4源代码中`development.xml`的消息部分，该文件路径为：`\src\modules\mavlink\mavlink\message_definitions\v1.0\development.xml`。

```xml
    <message id="11514" name="BATTERY_STATUS_DEMO">
      <description>Simple demo battery.</description>
      <field type="uint8_t" name="id" instance="true">Battery ID</field>
      <field type="int16_t" name="temperature" units="cdegC" invalid="INT16_MAX">Temperature of the whole battery pack (not internal electronics). INT16_MAX field not provided.</field>
      <field type="uint8_t" name="percent_remaining" units="%" invalid="UINT8_MAX">Remaining battery energy. Values: [0-100], UINT8_MAX: field not provided.</field>
    </message>
```

::: info
注意这是一个尚未实现的[BATTERY_STATUS_V2](https://mavlink.io/en/messages/development.html#BATTERY_STATUS_V2)消息的简化版本，使用了随机选择的未使用ID `11514`。
此处我们将消息放在`development.xml`中，适用于测试以及当该消息计划最终成为标准消息集的一部分时。但你也可以将[自定义消息](../mavlink/custom_messages.md)放在自己的方言文件中。
:::

为SITL构建PX4并确认相关消息已生成在`/build/px4_sitl_default/mavlink/development/mavlink_msg_battery_status_demo.h`。

由于`BatteryStatus`已存在，你无需执行任何操作来创建或构建它。

## 定义流类

首先在路径为 [/src/modules/mavlink/streams](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mavlink/streams) 的目录下，创建一个名为 `BATTERY_STATUS_DEMO.hpp` 的文件（以要流式传输的消息命名）。

在文件顶部添加 uORB 消息的头文件（所需的 MAVLink 头文件应已存在）：

```cpp
#include <uORB/topics/battery_status.h>
```

::: info
uORB 主题的蛇形命名头文件是在构建时从 uORB 文件的驼峰命名生成的。
:::

然后将以下流类定义复制到文件中：

```cpp
class MavlinkStreamBatteryStatusDemo : public MavlinkStream
{
public:
    static MavlinkStream *new_instance(Mavlink *mavlink)
    {
        return new MavlinkStreamBatteryStatusDemo(mavlink);
    }
    const char *get_name() const
    {
        return MavlinkStreamBatteryStatusDemo::get_name_static();
    }
    static const char *get_name_static()
    {
        return "BATTERY_STATUS_DEMO";
    }
    static uint16_t get_id_static()
    {
        return MAVLINK_MSG_ID_BATTERY_STATUS_DEMO;
    }
    uint16_t get_id()
    {
        return get_id_static();
    }
    unsigned get_size()
    {
        return MAVLINK_MSG_ID_BATTERY_STATUS_DEMO_LEN + MAVLINK_NUM_NON_PAYLOAD_BYTES;
    }

private:
    // 对 uORB 电池状态实例数组的订阅
    uORB::SubscriptionMultiArray<battery_status_s, battery_status_s::MAX_INSTANCES> _battery_status_subs{ORB_ID::battery_status};
    // 需要SubscriptionMultiArray订阅，因为电池有多个实例。
    // uORB::Subscription 用于订阅单实例主题

    /* 不允许复制此类 */
    MavlinkStreamBatteryStatusDemo(MavlinkStreamBatteryStatusDemo &);
    MavlinkStreamBatteryStatusDemo& operator = (const MavlinkStreamBatteryStatusDemo &);

protected:
    explicit MavlinkStreamBatteryStatusDemo(Mavlink *mavlink) : MavlinkStream(mavlink)
    {}

	bool send() override
	{
		bool updated = false;

		// 遍历 _battery_status_subs（电池状态实例数组的订阅）
		for (auto &battery_sub : _battery_status_subs) {
            // battery_status_s 是一个可以保存电池对象主题的结构体
			battery_status_s battery_status;

			// 如果状态已更改，则更新 battery_status 并发布
			if (battery_sub.update(&battery_status)) {
                // mavlink_battery_status_demo_t 是 MAVLink 消息对象
				mavlink_battery_status_demo_t bat_msg{};

				bat_msg.id = battery_status.id - 1;
				bat_msg.percent_remaining = (battery_status.connected) ? roundf(battery_status.remaining * 100.f) : -1;

				// 检查温度是否有效
				if (battery_status.connected && PX4_ISFINITE(battery_status.temperature)) {
					bat_msg.temperature = battery_status.temperature * 100.f;
				} else {
					bat_msg.temperature = INT16_MAX;
				}

                // 发送消息
				mavlink_msg_battery_status_demo_send_struct(_mavlink->get_channel(), &bat_msg);
				updated = true;
			}
		}

		return updated;
	}

};
```

大多数流类都非常相似（查看示例在 [/src/modules/mavlink/streams](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/mavlink/streams)）:

- 流类继承自 [`MavlinkStream`](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_stream.h) 并使用 `MavlinkStream<CamelCaseMessageName>` 的模式命名。
- `public` 定义是“近似模板”，允许 PX4 获取类的实例（`new_instance()`），然后使用它从 MAVLink 头文件中获取消息的名称、ID 和大小（`get_name()`、`get_name_static()`、`get_id_static()`、`get_id()`、`get_size()`）。
  对于自己的流类，这些可以复制并修改以匹配您的 MAVLink 消息的值。
- `private` 定义订阅需要发布的 uORB 主题。
  在这种情况下，uORB 主题有多个实例：每个电池一个实例。
  我们使用 `uORB::SubscriptionMultiArray` 来获取电池状态订阅的数组。

  此处我们还定义构造函数以防止复制该定义。

- `protected` 部分是执行核心工作的地方！

  在此处我们重写 `send()` 方法，将值从订阅的 uORB 主题复制到 MAVLink 消息的相应字段中，然后发送消息。

  在此特定示例中，我们有一个 uORB 实例数组 `_battery_status_subs`（因为我们有多个电池）。
  我们遍历该数组，并对每个订阅使用 `update()` 来检查相关电池实例是否已更改（并用当前数据更新结构体）。
  这允许我们仅在相关电池 uORB 主题更改时发送 MAVLink 消息：

  ```cpp
  // 用于保存当前主题数据的结构体。
  battery_status_s battery_status;

  // update() 会填充 battery_status 并在状态更改时返回 true
  if (battery_sub.update(&battery_status)) {
     // 使用 battery_status 填充消息并发送
  }
  ```

  如果希望无论数据是否更改都发送 MAVLink 消息，可以改用 `copy()`，如以下所示：

  ```cpp
  battery_status_s battery_status;
  battery_sub.copy(&battery_status);
  ```

  ::: info
  对于像 [VehicleStatus](../msg_docs/VehicleStatus.md) 这样的单实例主题，我们可以这样订阅：

  ```cpp
  // 创建订阅 _vehicle_status_sub
  uORB::Subscription _vehicle_status_sub{ORB_ID(vehicle_status)};
  ```

  然后可以以相同方式使用 update 或 copy 进行处理。

  ```cpp
  vehicle_status_s vehicle_status{}; // vehicle_status_s 是 uORB 主题的定义
  if (_vehicle_status_sub.update(&vehicle_status)) {
    // 使用更新后的数据进行操作
  }
  ```
  :::

接下来将新类包含到 `mavlink_messages.cpp` 并添加到流列表中：

在路径为 [/src/modules/mavlink/mavlink_messages.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_messages.cpp) 的文件中，将新类添加到流列表：

```cpp
#include "MavlinkStreamBatteryStatusDemo.hpp"

...

const MavlinkStream *Mavlink::get_streams() const
{
    static const MavlinkStream *streams[] = {
        ...
        new MavlinkStreamBatteryStatusDemo(mavlink),
    };
    return streams;
}
```

## 默认流式传输

通过构建将消息默认流式传输的最简单方法是将其添加到[mavlink_main.cpp](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/mavlink_main.cpp)中适当的消 息组。

在文件中搜索可以找到以 switch 语句定义的消息组：

- `MAVLINK_MODE_NORMAL`: 流式传输到地面控制站(GCS)
- `MAVLINK_MODE_ONBOARD`: 通过高速链路(如以太网)流式传输到计算单元
- `MAVLINK_MODE_ONBOARD_LOW_BANDWIDTH`: 流式传输到计算单元用于低流量链路重定向，如GCS
- `MAVLINK_MODE_GIMBAL`: 流式传输到云台
- `MAVLINK_MODE_EXTVISION`: 流式传输到外部视觉系统
- `MAVLINK_MODE_EXTVISIONMIN`: 通过低速链路流式传输到外部视觉系统
- `MAVLINK_MODE_OSD`: 流式传输到OSD，如FPV头显
- `MAVLINK_MODE_CUSTOM`: 默认不传输。用于通过MAVLink配置流式传输
- `MAVLINK_MODE_MAGIC`: 同 `MAVLINK_MODE_CUSTOM`
- `MAVLINK_MODE_CONFIG`: 通过USB传输，速率高于 `MAVLINK_MODE_NORMAL`
- `MAVLINK_MODE_MINIMAL`: 流式传输最小消息集。通常用于差的遥测链路
- `MAVLINK_MODE_IRIDIUM`: 流式传输到铱星卫星电话

通常您会在GCS上测试，因此可以通过 `MAVLINK_MODE_NORMAL` 的 case 使用 `configure_stream_local()` 方法添加消息。例如，以5Hz传输CA_TRAJECTORY：

```cpp
	case MAVLINK_MODE_CONFIG: // USB
		// 注意：需要低延迟的消息排在前面
		...
		configure_stream_local("BATTERY_STATUS_DEMO", 5.0f);
        ...
```

也可以通过在[startup script](../concept/system_startup.md)中调用[mavlink](../modules/modules_communication.md#mavlink)模块的 `stream` 参数来添加流式传输。例如，您可能需要向 [/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink](https://github.com/PX4/PX4-Autopilot/blob/main/ROMFS/px4fmu_common/init.d-posix/px4-rc.mavlink) 添加以下行，以50Hz在UDP端口14556传输 `BATTERY_STATUS_DEMO`（`-r` 配置流式传输速率，`-u` 标识UDP端口14556上的MAVLink通道）：

```sh
mavlink stream -r 50 -s BATTERY_STATUS_DEMO -u 14556
```

## 按请求流式传输

某些消息仅在连接特定硬件或在其他特定情况下时才需要一次。  
为了避免用不需要的消息堵塞通信链路，默认情况下可能不会以低速率流式传输所有消息。

如果需要，地面控制站（GCS）或其他MAVLink API可以使用 [MAV_CMD_SET_MESSAGE_INTERVAL](https://mavlink.io/en/messages/common.html#MAV_CMD_SET_MESSAGE_INTERVAL) 请求特定消息以特定速率进行流式传输。  
特定消息可以使用 [MAV_CMD_REQUEST_MESSAGE](https://mavlink.io/en/messages/common.html#MAV_CMD_REQUEST_MESSAGE) 仅请求一次。