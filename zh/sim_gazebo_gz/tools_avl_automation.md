# 高级升阻（AVL）自动化工具

Gazebo [Advanced Plane](../sim_gazebo_gz/vehicles.md#advanced-plane) 机体模型使用 _Advanced Lift Drag_ 插件来模拟机体升力和阻力行为。  
该工具允许您计算创建 _Advanced Lift Drag_ 插件所需的参数以适配您特定的机体。

您只需为每个机翼剖面提供少量参数，工具将利用这些信息调用Athena Lattice Vortex (AVL) 进行必要的计算。  
计算结果会自动写入提供的插件模板中，之后可复制粘贴到模型或世界 sdf 文件中。

## 安装

要设置该工具：

1. 从 <https://web.mit.edu/drela/Public/web/avl/> 下载 AVL 3.36。
   页面中间位置可找到 AVL 3.36 对应的文件。
1. 下载完成后，提取 AVL 并通过以下命令将其移动到主目录：

   ```sh
   sudo tar -xf avl3.36.tgz
   mv ./Avl /home/
   ```

1. 按照 `./Avl` 目录中的 **index.md** 完成 AVL 的设置过程（这需要你设置 `plotlib` 和 `eispack` 库）。
   我们建议使用 `gfortran` 编译选项，这可能需要你安装 `gfortran`。
   在 Ubuntu 上可通过运行以下命令完成：

   ```sh
   sudo apt update
   sudo apt install gfortran
   ```

   在运行 AVL 的 Makefile 时，可能会遇到提示目录缺失的 `Error 1` 错误。
   这不会影响 AVL 的正常使用。

完成 AVL 说明文件中的流程后，AVL 即可使用。
无需再对 AVL 或该工具进行其他设置。

如果你想更改 AVL 目录的位置，只需向 `input_avl.py` 文件传递 `--avl_path` 标志，并将标志值设为你希望的目录位置（别忘了在路径最后加上"/"）。
执行此操作将自动调整必要的路径。

## 运行AVL

一个标准飞机模板已通过`input.yml`文件形式提供，该模板实现了带有两个副翼、一个升降舵和一个方向舵的标准飞机。  
该示例模板可通过以下方式运行：`python input_avl.py --yaml_file input.yml`。

要针对您的飞机运行该工具：

1. 将示例`input.yml`复制为`<your_custom_yaml_file>.yml`并修改以匹配您所需的飞机
1. 在您的yml文件上运行该工具：

   ```sh
   python input_avl.py <your_custom_yaml_file>.yml
   ```

   注意，您的Python环境必须包含`yaml`和`argparse`包。

1. 该工具会提示输入一系列机体特定参数以定义飞机的几何形状和物理特性。  
   您可以选择：
   - 使用预定义模型模板（如塞斯纳飞机或垂直起降飞机），这些模板具有已知数量的控制面，只需修改部分物理特性
   - 定义一个完全自定义的模型

脚本执行完成后，生成的`.avl`、`.sdf`文件以及所提议控制面的示意图将保存在`<your-plane-name>`目录中。  
sdf文件是生成的Advanced Lift Drag插件，可复制粘贴到`model.sdf`文件中，然后在Gazebo中运行。

## 功能

**input_avl.py** 文件接收用户提供的参数，并生成可被 AVL（该程序）读取的 .avl 文件。  
此过程在 **process.sh** 文件中完成。

AVL 生成的输出将保存在两个文件中：**custom_vehicle_body_axis_derivatives.txt** 和 **custom_vehicle_stability_derivatives.txt**。  
这两个文件包含用于填充 Advanced Lift Drag 插件所需的参数。

最终，**avl_out_parse.py** 读取生成的 .txt 文件，并将参数分配到 sdf 中的对应元素。

生成的 Advanced Lift Drag 插件（`<custom_plane>.sdf`）可复制到 Gazebo 使用的特定 **model.sdf** 文件中。

## 可用性

当前实现提供了一个最小可运行示例。  
通过根据需求调整沿展向和弦向的涡量数量，可以实现更精确的测量。  
一个良好的起点可参考此处：<https://www.redalyc.org/pdf/6735/673571173005.pdf>。  

通过使用更多分段，可以更精确地建模机体。  
在当前的.yml文件中，每个表面仅定义了左右边缘，形成一个分段，但代码支持扩展到任意数量的分段。  

::: info  

- 在AVL中，控制面始终从左到右定义。  
  这意味着你需要先提供表面的左边缘，再提供右边缘。  
  如果顺序相反，表面将被倒置定义。  
- 该工具设计为每种机体最多仅支持两个控制面。  
  超过该数量可能导致异常行为。  
- 另一个重要点是这些脚本使用了match, case语法，该语法仅在Python 3.10版本中引入。  
- AVL的主要参考资源可访问：<https://web.mit.edu/drela/Public/web/avl/AVL_User_Primer.pdf>。  
  该文档由AVL的开发者编写，包含定义控制面所需的所有变量。  
- AVL无法预测失速值，因此需要通过其他方式计算/估算。  
  在当前实现中，失速值的默认值来自PX4的Advanced Plane。  
  对于新的/不同的模型，这些值需要相应修改。  
  :::

## 参数分配

以下是输出端参数分配的完整列表，以及它们在AVL中对应的来源文件。
Advanced Lift Drag Plugin 插件中包含关于这些参数作用的更详细说明。

::: info
这些参数尚未经过专家验证，建议在插件中进行核对。
:::

从稳定性导数日志文件中获取以下Advanced Lift Drag Plugin参数：

| AVL中的名称 | Advanced Lift Drag Plugin中的名称 | 描述                                             |
| ----------- | --------------------------------- | ------------------------------------------------- |
| Alpha       | alpha                             | 攻角                                             |
| Cmtot       | Cem0                              | 零攻角时的俯仰力矩系数                           |
| CLtot       | CL0                               | 零攻角时的升力系数                               |
| CDtot       | CD0                               | 零攻角时的阻力系数                               |
| CLa         | CLa                               | dCL/da（CL-攻角曲线的斜率）                      |
| CYa         | CYa                               | dCy/da（侧向力相对于攻角的斜率）                |
| Cla         | Cell                              | dCl/da（滚转力矩相对于攻角的斜率）              |
| Cma         | Cema                              | dCm/da（俯仰力矩相对于攻角的斜率-失速前）       |
| Cna         | Cena                              | dCn/da（偏航力矩相对于攻角的斜率）              |
| CLb         | CLb                               | dCL/dbeta（升力系数相对于侧滑角的斜率）          |
| CYb         | CYb                               | dCY/dbeta（侧向力相对于侧滑角的斜率）            |
| Clb         | Cell                              | dCl/dbeta（滚转力矩相对于侧滑角的斜率）          |
| Cmb         | Cemb                              | dCm/dbeta（俯仰力矩相对于侧滑角的斜率）          |
| Cnb         | Cenb                              | dCn/dbeta（偏航力矩相对于侧滑角的斜率）          |

从机体轴导数日志文件中获取以下Advanced Lift Drag Plugin参数：

| AVL中的名称 | Advanced Lift Drag Plugin中的名称 | 描述                                               |
| ----------- | --------------------------------- | --------------------------------------------------- |
| e           | eff                               | 机翼效率（三维机翼的奥斯瓦尔德效率因子）           |
| CXp         | CDp                               | dCD/dp（阻力系数相对于滚转率的斜率）               |
| CYp         | CYp                               | dCY/dp（侧向力相对于滚转率的斜率）                 |
| CZp         | CLp                               | dCL/dp（升力系数相对于滚转率的斜率）               |
| Clp         | Cellp                             | dCl/dp（滚转力矩相对于滚转率的斜率）               |
| Cmp         | Cemp                              | dCm/dp（俯仰力矩相对于滚转率的斜率）               |
| Cmp         | Cenp                              | dCn/dp（偏航力矩相对于滚转率的斜率）               |
| CXq         | CDq                               | dCD/dq（阻力系数相对于俯仰率的斜率）               |
| CYq         | CYq                               | dCY/dq（侧向力相对于俯仰率的斜率）                 |
| CZq         | CLq                               | dCL/dq（升力系数相对于俯仰率的斜率）               |
| Clq         | Cellq                             | dCl/dq（滚转力矩相对于俯仰率的斜率）               |
| Cmq         | Cemq                              | dCm/dq（俯仰力矩相对于俯仰率的斜率）               |
| Cnq         | Cenq                              | dCn/dq（偏航力矩相对于俯仰率的斜率）               |
| CXr         | CDr                               | dCD/dr（阻力系数相对于偏航率的斜率）               |
| CYr         | CYr                               | dCY/dr（侧向力相对于偏航率的斜率）                 |
| CZr         | CLr                               | dCL/dr（升力系数相对于偏航率的斜率）               |
| Clr         | Cellr                             | dCl/dr（滚转力矩相对于偏航率的斜率）               |
| Cmr         | Cemr                              | dCm/dr（俯仰力矩相对于偏航率的斜率）               |
| Cnr         | Cenr                              | dCn/dr（偏航力矩相对于偏航率的斜率）               |

此外，每个控制面还包含六个独立参数，这些参数同样来源于此日志文件。
下方`{i}`的取值范围为1到模型中不同控制面类型的数量。

| AVL中的名称 | Advanced Lift Drag Plugin中的名称 | 描述                                                   |
| ----------- | --------------------------------- | ------------------------------------------------------- |
| CXd{i}      | CD_ctrl                           | 控制面偏转对阻力的影响                                 |
| CYd{i}      | CY_ctrl                           | 控制面偏转对侧向力的影响                               |
| CZd{i}      | CL_ctrl                           | 控制面偏转对升力的影响                                 |
| Cld{i}      | Cell_ctrl                         | 控制面偏转对滚转力矩的影响                             |
| Cmd{i}      | Cem_ctrl                          | 控制面偏转对俯仰力矩的影响                             |
| Cnd{i}      | Cen_ctrl                          | 控制面偏转对偏航力矩的影响                             |