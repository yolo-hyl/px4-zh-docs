# Gazebo模型仓库 (PX4-gazebo-models)

[PX4-gazebo-models](https://github.com/PX4/PX4-gazebo-models) 仓库用于存储所有由 PX4 支持的 [Gazebo](../sim_gazebo_gz/index.md) 模型和场景。

- 模型存储在 `/models` 目录，场景存储在 `/worlds` 目录。
- [simulation-gazebo](https://github.com/PX4/PX4-gazebo-models/blob/main/simulation-gazebo) Python脚本用于[starting Gazebo in standalone mode](../sim_gazebo_gz/index.md#standalone-mode)。

`PX4-gazebo-models` 仓库以子模块形式包含在 PX4 中，当使用常规 `make` 目标时（如 `make px4_sitl gz_x500`），所有模型默认可用。

对于 Gazebo [standalone simulations](../sim_gazebo_gz/index.md#standalone-mode)，首先需要获取 [simulation-gazebo](https://github.com/PX4/PX4-gazebo-models/blob/main/simulation-gazebo) Python脚本，如果 `~/.simulation-gazebo` 目录不存在，该脚本会自动将模型和场景下载到该目录。

## simulation-gazebo (独立模拟启动脚本)

[simulation-gazebo](https://github.com/PX4/PX4-gazebo-models/blob/main/simulation-gazebo) Python脚本用于在[独立模式](../sim_gazebo_gz/index.md#standalone-mode)下启动Gazebo。
该脚本默认可与同一主机上的PX4 SITL实例通信。
如果正确设置脚本参数，也可与同一网络内任意机器的PX4实例通信。

`simulation-gazebo`脚本无需任何额外库，开箱即用。

### 基本用法

默认的`simulation-script`可通过以下方式运行：

```sh
python simulation-gazebo
```

首次调用时（或更精确地说，当未检测到`.simulation-gazebo`目录时），该脚本会从[PX4 Gazebo模型仓库](https://github.com/PX4/PX4-gazebo-models)下载模型和世界文件到`~/.simulation-gazebo`目录的子文件夹中。
随后将使用默认灰色飞机世界启动一个_gz-server_实例。

如果检测到`.simulation-gazebo`文件夹，构建系统将不会自动更新本地副本，但可以通过向脚本传递`overwrite`标志强制更新到最新模型和机体。
命令示例如下：

```sh
python simulation-gazebo --overwrite
```

可通过以下几种方式将PX4启用的机体连接到_gz-server_实例：

- 在新终端中运行 `PX4_GZ_STANDALONE=1 make px4_sitl gz_<vehicle>` 启动PX4，您将观察到机体在Gazebo中出现。

- Gazebo也具有自己的内置"resource spawner"。
  可通过点击Gazebo GUI右上角的三个点调用。
  在此输入"resource spawner"，并在"Fuel resources"下添加所有者"px4"。
  然后可将任意PX4模型拖放到您的模拟中。

  ::: info
  这些模型来自名为[Gazebo Fuel](https://app.gazebosim.org/dashboard)的网络服务器，该服务器本质上充当Gazebo中所有类型模型和世界的在线数据库。
  :::

  拖放选定机体到Gazebo后，通过以下命令启动PX4 SITL：

  ```sh
  PX4_SYS_AUTOSTART=<airframe-number-of-choice> PX4_GZ_MODEL_NAME=<vehicle-of-choice> ./build/px4_sitl_default/bin/px4`
  ```

  这将使PX4 SITL连接到正在运行的Gazebo实例。

所有适用于PX4的功能和灵活性均直接应用于Gazebo实例。

### 命令行参数

以下参数可传递给`simulation-gazebo`脚本：

- `--world`
  字符串变量，指定运行模拟世界的sdf文件。
  默认参数为"default"，指向默认世界。

- `--gz_partition`
  字符串变量，设置Gazebo运行的分区（更多信息[here](https://gazebosim.org/api/transport/13/envvars.html)）

- `--gz_ip`
  字符串变量，设置出站网络接口的IP（更多信息[here](https://gazebosim.org/api/transport/13/envvars.html)）

- `--interactive` 布尔变量，要求代码以交互模式运行，允许自定义`--model_download_source`路径。
  如果未设置，`--model_download_source`将仅从默认Github仓库下载。

- `--model_download_source`
  字符串变量，设置模型导入的目录路径。
  当前仅支持本地文件目录或http地址。
  源路径应以压缩模型文件结尾（例如：https://path/to/simulation/models/models.zip）。

- `--model_store`
  字符串变量，设置模型存储目录的路径。
  这是`model_download_source`提供的zip文件将存放的位置。

- `--overwrite`
  布尔变量，表示`.simulation-gazebo`应使用Gazebo模型仓库中的世界和机体数据更新。

- `--dryrun`
  布尔变量，运行测试用例时可设置。
  它不会提供任何交互性，也不会启动gz-server实例。

`simulation-gazebo`的运行不需要这些参数。
当需要自定义模型下载、其他世界，或需要在单独主机上运行Gazebo和PX4时才需要使用。

## 示例：在一台主机上运行多个终端

本示例说明如何通过两个终端在一台主机上以独立模式运行PX4。

1. 在其中一个终端中运行

   ```sh
   PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 PX4_GZ_WORLD=windy ./build/px4_sitl_default/bin/px4
   ```

1. 在第二个终端窗口中运行：

   ```sh
   python3 /path/to/simulation-gazebo --world windy
   ```

为使此示例正常工作，无需向 simulation-gazebo 脚本传递额外参数，因为所有 Gazebo 节点都在同一台主机上运行。
请参见下面的示例，了解涉及不同主机的更复杂场景。

## 示例：运行多个主机

以下示例将说明如何设置分布式系统，在网络中的一个主机上运行PX4（称为"PX4-host"），在另一个主机上运行Gazebo（称为"Gazebo-host"）。这将导致两个Gazebo节点在同一网络的不同主机上运行，并通过`gz-transport`库进行通信。

我们首先需要确定可以在两个主机上发送消息的IP地址。

在PX4主机上，运行以下命令：

```sh
sudo apt update
sudo apt install iproute2
```

然后输入：

```sh
ip a
```

如果你通过WiFi连接到网络，那么目标地址通常会以类似`wlp12345`的名称出现。通过记录`inet`旁的数字获取该接口的IPv4地址，该地址应为四个由点分隔的数字，后跟斜杠和另一个数字。例如有效IPv4地址为：`192.168.24.89/24`。

你只需要前四个数字。例如，PX4主机的IP地址为`192.168.24.89`。注意如果通过以太网连接，网络接口可能以`eth`或`en`开头，但并非标准化。

在Gazebo主机上重复相同操作并记录第二个IPv4地址。在此示例中我们使用`192.168.24.107`。

现在可以开始设置两个主机。首先设置PX4主机：

```sh
GZ_PARTITION=relay GZ_RELAY=192.168.24.107 GZ_IP=192.168.24.89 PX4_GZ_STANDALONE=1 PX4_SYS_AUTOSTART=4001 PX4_SIM_MODEL=gz_x500 PX4_GZ_WORLD=baylands ./build/px4_sitl_default/bin/px4
```

环境变量说明：

- `GZ_PARTITION`声明两个Gazebo节点运行的分区名称。此值**必须**在所有连接节点间保持一致。
- `GZ_RELAY`指向目标主机的IP地址（在此示例中为Gazebo主机）。此环境变量用于连接两个节点。注意该连接是双向的，因此只需在单个主机上设置`GZ_RELAY`。
- `GZ_IP`告诉当前主机使用哪个网络接口发送消息。当发布主题或服务时该参数是必需的。

接下来设置Gazebo主机。注意实际设置顺序（先PX4主机还是先Gazebo主机）并不重要，两个节点会持续寻找其他Gazebo节点直到找到。在Gazebo主机的终端运行：

```sh
python3 /path/to/simulation-gazebo --gz_partition relay --gz_ip 192.168.24.107 --world baylands
```

此处我们将环境变量作为参数传递。注意`--world`值必须相同才能建立连接。如果在两个主机上分别设置`baylands`和`default`，则节点将无法连接。

如果操作成功，两个主机将建立连接，你可以在PX4主机的命令行中飞行机体。

此外，你也可以设置QGC并以该方式飞行机体。还可以通过设置`-i`标志将多个PX4主机连接到同一Gazebo主机，详情请参阅[多机体仿真](./multi_vehicle_simulation.md)页面。关于环境变量的更多信息，请参阅[gz-transport文档](https://gazebosim.org/api/transport/12/envvars.html)。

通过VPN（因此涉及多个网络）建立连接也是可能的，但目前尚未文档化。

## 工作流程

当一个分支被合并到主分支（这可能包括模型添加、删除或参数更改等操作）时，所有发生过任何更改的模型将自动更新并上传到PX4在[Gazebo Fuel](https://app.gazebosim.org/PX4)上的账户。  
此外，还有一套工作流程可用于验证提供的任何sdf文件的有效性。