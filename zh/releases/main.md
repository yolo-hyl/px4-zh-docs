# PX4-Autopilot 主版本说明

<Badge type="danger" text="Alpha" />

<script setup>
import { useData } from 'vitepress'
const { site } = useData();
</script>

<div v-if="site.title !== 'PX4 Guide (main)'">
  <div class="custom-block danger">
    <p class="custom-block-title">此页面位于版本分支上，因此可能已过时。 <a href="https://docs.px4.io/main/en/releases/main.html">查看最新版本</a>.</p>
  </div>
</div>

自上一个主要版本 ([PX v1.16](../releases/1.16.md)) 以来，PX4 `main` 分支包含的变更内容。

::: warning
PX4 v1.16 正在候选版本测试中，尚未正式发布。
请更新这些说明以包含将包含在 `main` 中但不在 PX4 v1.16 版本中的功能。
:::

## 升级前必读

TBD …

请继续阅读 [升级指南](#升级指南)。

## 主要变更

- TBD

## 升级指南

## 其他变更

### 硬件支持

- TBD

### 通用功能

- [QGroundControl Bootloader Update](../advanced_config/bootloader_update.md#qgc-bootloader-update-sys-bl-update) 通过 [SYS_BL_UPDATE](../advanced_config/parameter_reference.md#SYS_BL_UPDATE) 参数已重新启用，此前在多个版本中出现故障。 ([PX4-Autopilot#25032: build: romf: fix generation of rc.board_bootloader_upgrade](https://github.com/PX4/PX4-Autopilot/pull/25032))。

### 控制

- TBD

### 估计器

- TBD

### 传感器

- TBD

### 仿真

- TBD

### 以太网

- TBD

### uXRCE-DDS / ROS2

- [PX4 ROS 2 Interface Library](../ros2/px4_ros2_control_interface.md) 支持 [固定翼横向/纵向设定值](../ros2/px4_ros2_control_interface.md#fixed-wing-lateral-and-longitudinal-setpoint-fwlaterallongitudinalsetpointtype) (`FwLateralLongitudinalSetpointType`) 和 [VTOL 转换](../ros2/px4_ros2_control_interface.md#controlling-a-vtol)。 ([PX4-Autopilot#24056](https://github.com/PX4/PX4-Autopilot/pull/24056))。

### MAVLink

- TBD

### 多旋翼

- TBD

### VTOL

- TBD

### 固定翼

- TBD

### 越野车

- TBD

### ROS 2

- TBD