

# 硬故障调试

硬故障是CPU执行无效指令或访问无效内存地址时的状态。当RAM中的关键区域被损坏时，可能会发生这种情况。

## 视频

以下视频演示了使用 Eclipse 和 JTAG 调试器在 PX4 上进行硬故障调试。  
该视频发布于 PX4 开发者大会 2019。

<lite-youtube videoid="KZkAM_PVOi0" title="Hardfault debugging on PX4"/>

---

以下视频介绍了通过 GDB 进行 PX4 高级调试的可用工具（包括硬故障调试）。  
该视频发布于 PX4 开发者大会 2023。

<lite-youtube videoid="1c4TqEn3MZ0" title="Debugging PX4 - Niklas Hauser, Auterion AG"/>

## NuttX 中的硬故障调试

一个典型的会导致硬故障的场景是处理器堆栈溢出。NuttX 中维护了两个独立的堆栈：IRQ 堆栈（中断服务程序使用的共享堆栈）和用户堆栈（任务/线程私有堆栈）。当发生堆栈溢出时，系统会触发硬故障并记录相关上下文信息。

在调试日志中可以看到硬故障发生时的寄存器状态（如 R0-R12、xPSR 等），以及最后执行的内存地址。要解析这些信息，需要使用 GDB 调试工具加载完全一致的二进制文件：

```sh
arm-none-eabi-gdb build/px4_fmu-v2_default/px4_fmu-v2_default.elf
```

在 GDB 提示符下，从 R8 寄存器中保存的最后指令地址开始分析（地址以 `0x080` 开头的表示 flash 内存地址）。例如通过以下命令定位具体代码行：

```sh
(gdb) info line *0x0808439f
Line 77 of "../src/modules/systemlib/mavlink_log.c" starts at address 0x8084398 <mavlink_vasprintf+36>
   and ends at 0x80843a0 <mavlink_vasprintf+44>.
```

```sh
(gdb) info line *0x08087c4e
Line 311 of "../src/modules/uORB/uORBDevices_nuttx.cpp"
   starts at address 0x8087c4e <uORB::DeviceNode::publish(orb_metadata const*, void*, void const*)+2>
   and ends at 0x8087c52 <uORB::DeviceNode::publish(orb_metadata const*, void*, void const*)+6>.
```

完整的硬故障上下文包括：
```
R0: 20000f48 0a91ae0c 20020d00 20020d00 2001f578 080ca038 000182b8 20017cc0
R8: 2001ed20 2001f4e8 2001ed20 00000005 20020d20 20020ce8 0808439f 08087c4e
xPSR: 61000000 BASEPRI: 00000000 CONTROL: 00000000
EXC_RETURN: ffffffe9
```

通过分析这些地址和代码行，可以定位到硬故障发生的具体位置。例如在示例中，硬故障发生前正在执行 `mavlink_log.c` 的日志发布操作和 `uORB` 的消息发布函数。