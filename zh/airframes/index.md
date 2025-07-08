# 机体类型与配置

PX4 支持多种类型的机体，包括不同配置的多旋翼、固定翼飞机、垂直起降（VTOL）机体、地面车辆等。

本节解释了如何为每种类型组装、配置和调校基于 PX4 的自动驾驶系统（大部分配置流程对所有类型通用）。

::: info
[基础概念 > 无人机类型](../getting_started/px4_basic_concepts.md#drone-types) 提供了关于机体类型及其适用场景的高层级信息。
:::

## 支持的机体

具有维护人员且经过良好测试和官方支持的机体类型包括：

- [多旋翼](../frames_multicopter/index.md)（三旋翼、四旋翼、六旋翼、八旋翼，甚至[全向旋翼](../frames_multicopter/omnicopter.md)）
- [固定翼飞机](../frames_plane/index.md)
- [垂直起降（VTOL）](../frames_vtol/index.md)：[标准 VTOL](../frames_vtol/standardvtol.md)，[尾坐式 VTOL](../frames_vtol/tailsitter.md)，[倾转旋翼 VTOL](../frames_vtol/tiltrotor.md)

## 实验性机体

实验性机体是指以下类型的机体：

- 没有维护人员
- 核心开发团队不会定期测试
- 可能未在持续集成（CI）中测试
- 可能缺少生产就绪机体所需的功能
- 可能不支持该类型机体的某些通用配置

属于实验性机体的类型包括：

- [飞艇](../frames_airship/index.md)
- [自转旋翼机](../frames_autogyro/index.md)
- [气球](../frames_balloon/index.md)
- [直升机](../frames_helicopter/index.md)
- [地面车](../frames_rover/index.md)
- [潜艇](../frames_sub/index.md)

::: info
欢迎维护人员志愿者、[新功能贡献](../contribute/index.md)、新机体配置或其他改进！
:::

## 其他机体

完整的机体类型及配置列表请参考[Airframes Reference](../airframes/airframe_reference.md)。