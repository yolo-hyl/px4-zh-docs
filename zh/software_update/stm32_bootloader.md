# STM32 Bootloader

PX4 Bootloader的代码可以从Github的[Bootloader](https://github.com/px4/bootloader)仓库获取。

## 支持的板卡

* FMUv2 (Pixhawk 1, STM32F4)
* FMUv3 (Pixhawk 2, STM32F4)
* FMUv4 (Pixracer 3 和 Pixhawk 3 Pro, STM32F4)
* FMUv5 (Pixhawk 4, STM32F7)
* TAPv1 (TBA, STM32F4)
* ASCv1 (TBA, STM32F4)

## 构建Bootloader

```sh
git clone https://github.com/PX4/Bootloader.git
cd Bootloader
git submodule init
git submodule update
make
```

完成此步骤后，所有支持板卡的elf文件将出现在Bootloader目录中。

## 烧录Bootloader

:::warning
某些板卡的正确电源顺序对JTAG/SWD访问至关重要。请严格按照以下步骤操作。
:::

以下说明适用于Blackmagic/Dronecode探针。其他JTAG探针需要类似但不同的步骤。尝试烧录Bootloader的开发者应具备相关知识。如果不知道如何操作，建议重新考虑是否需要修改Bootloader。

操作顺序为：
1. 断开JTAG电缆
2. 连接USB电源线
3. 连接JTAG电缆

### Black Magic / Dronecode 探针

#### 使用正确的串口

* 在LINUX上: `/dev/serial/by-id/usb-Black_Sphere_XXX-if00`
* 在MAC OS上: 确保使用cu.xxx端口而非tty.xxx端口: `tar ext /dev/tty.usbmodemDDEasdf`

```sh
arm-none-eabi-gdb
  (gdb) tar ext /dev/serial/by-id/usb-Black_Sphere_XXX-if00
  (gdb) mon swdp_scan
  (gdb) attach 1
  (gdb) mon option erase
  (gdb) mon erase_mass
  (gdb) load tapv1_bl.elf
        ...
        Transfer rate: 17 KB/sec, 828 bytes/write.
  (gdb) kill
```

### J-Link

此部分说明适用于[J-Link GDB server](https://www.segger.com/jlink-gdb-server.html)。

#### 先决条件

从[Segger官网](https://www.segger.com/downloads/jlink)下载并安装J-Link软件。

#### 运行JLink GDB服务端

以下命令用于运行使用STM32F427VI SoC的飞控：

```sh
JLinkGDBServer -select USB=0 -device STM32F427VI -if SWD-DP -speed 20000
```

`--device`/SoC对应常见目标为：

* **FMUv2, FMUv3, FMUv4, aerofc-v1, mindpx-v2:** STM32F427VI
* **px4_fmu-v4pro:** STM32F469II
* **px4_fmu-v5:** STM32F765II
* **crazyflie:** STM32F405RG


#### 连接GDB

```sh
arm-none-eabi-gdb
  (gdb) tar ext :2331
  (gdb) load aerofcv1_bl.elf
```

### 故障排查

如果上述任何命令未找到，说明你未使用Blackmagic探针或其软件已过时。首先升级探针上的软件。

如果出现此错误信息：
```
Error erasing flash with vFlashErase packet
```

断开目标设备（保持JTAG连接），然后运行 

```sh
mon tpwr disable
swdp_scan
attach 1
load tapv1_bl.elf
```
这将关闭目标供电并尝试再次烧录。