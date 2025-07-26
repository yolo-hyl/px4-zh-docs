# Gazebo 插件

Gazebo 插件通过自定义功能扩展模拟器的默认功能。它们可以附加到不同实体类型上，允许你添加新传感器、修改世界物理属性，或与模拟环境进行交互。

## 插件类型

插件可以附加到以下实体类型：

- **世界** - 全局模拟行为
- **模型** - 特定模型功能
- **传感器** - 自定义传感器实现
- **角色** - 动态实体行为

## 支持的插件

PX4 当前支持以下插件：

- [OpticalFlowSystem](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_plugins/optical_flow): 提供基于OpenCV的光流计算的光流传感器模拟。
- [GstCameraSystem](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_plugins/gstreamer): 通过UDP (RTP/H.264)或RTMP流式传输摄像头画面，支持可选的NVIDIA CUDA硬件加速。
- [MovingPlatformController](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_plugins/moving_platform_controller): 控制移动平台(船只、卡车等)以实现起降场景。包含可配置的速度、航向和随机波动。

## 插件注册

插件必须在 [server.config](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/gz_bridge/server.config) 文件中注册才能在你的世界中使用：

```xml
<server_config>
  <plugins>
    <!-- Core Gazebo systems -->
    <plugin entity_name="*" entity_type="world" filename="gz-sim-physics-system" name="gz::sim::systems::Physics"/>

    <!-- Custom PX4 plugins -->
    <plugin entity_name="*" entity_type="world" filename="libOpticalFlowSystem.so" name="custom::OpticalFlowSystem"/>
    <plugin entity_name="*" entity_type="world" filename="libGstCameraSystem.so" name="custom::GstCameraSystem"/>
  </plugins>
</server_config>
```

## 创建自定义插件

开发新插件时：

1. **遵循插件架构** - 实现所需接口。

   可以从 [模板插件](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_plugins/template_plugin) 开始，它是一个仅实现 `ISystemPreUpdate` 和 `ISystemPostUpdate` 的简单示例。
   接口规范在官方 [Gazebo 文档](https://gazebosim.org/api/sim/9/createsystemplugins.html) 中指定。

2. **注册你的插件** - 将其添加到 [server.config](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/simulation/gz_bridge/server.config) 以启用。
3. **使用自定义命名空间** - 遵循 `custom::YourPluginName` 的命名模式。

### 示例插件结构

```cpp
class YourCustomSystem :
    public gz::sim::System,
    public gz::sim::ISystemPreUpdate,
    public gz::sim::ISystemPostUpdate
{
public:
    void PreUpdate(const gz::sim::UpdateInfo &_info,
                   gz::sim::EntityComponentManager &_ecm) final;
    void PostUpdate(const gz::sim::UpdateInfo &_info,
                    const gz::sim::EntityComponentManager &_ecm) final;
};

// 插件注册
GZ_ADD_PLUGIN(YourCustomSystem, gz::sim::System,
              YourCustomSystem::ISystemPreUpdate,
              YourCustomSystem::ISystemPostUpdate)
GZ_ADD_PLUGIN_ALIAS(YourCustomSystem, "custom::YourCustomSystem")
```

## 启用插件

对于世界插件，只需 [注册插件](#插件注册) (将其添加到 `server.config`)。
之后它将对所有世界和机体可用。

添加机体模型/传感器插件的流程尚未文档化。
可通过 [PX4-Autopilot#2493](https://github.com/PX4/PX4-Autopilot/issues/24939) 跟踪进展。

## 资源

- **PX4 插件**: [仓库源代码](https://github.com/PX4/PX4-Autopilot/tree/main/src/modules/simulation/gz_plugins)
- **官方 Gazebo 文档**: [系统插件指南](https://gazebosim.org/api/sim/9/createsystemplugins.html)
- **服务器配置**: [配置参考](https://gazebosim.org/api/sim/9/server_config.html)
- **PX4 Gazebo-classic 插件**: [PX4 Gazebo Classic 插件](https://github.com/PX4/PX4-SITL_gazebo-classic/tree/main/src)

::: info
现代 Gazebo 的插件仍在发展中。插件系统与 Gazebo Classic 不同。
:::