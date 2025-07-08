# 常见问题

## 构建错误

### Flash溢出

可加载到开发板的代码量受其Flash存储容量限制。  
当添加额外模块或代码时，新增内容可能超过Flash存储容量。  
这将导致"Flash溢出"。上游版本总能成功构建，但具体是否会溢出取决于开发者添加的内容。

```sh
region `flash' overflowed by 12456 bytes
```

解决方法：使用更新的硬件，或从构建中移除对当前使用场景非必需的模块。  
配置文件存储在 **/PX4-Autopilot/boards/px4** 目录中（例如 [PX4-Autopilot/boards/px4/fmu-v5/default.px4board](https://github.com/PX4/PX4-Autopilot/blob/main/boards/px4/fmu-v5/default.px4board)）。  
移除模块只需注释掉对应行：

```cmake
#tune_control
```

#### 识别大内存占用模块

以下命令将列出最大的静态内存分配：

```sh
arm-none-eabi-nm --size-sort --print-size --radix=dec build/px4_fmu-v5_default/px4_fmu-v5_default.elf | grep " [bBdD] "
```

## USB错误

### 上传永远失败

在Ubuntu系统上卸载调制解调器管理器：

```sh
sudo apt-get remove modemmanager
```