# 外部模块（Out-of-Tree）

外部模块为开发者提供了一种便捷的机制，用于管理/分组他们希望添加到（或更新到）PX4固件中的专有模块。  
外部模块可以使用与内部模块相同的头文件，并可通过uORB与内部模块进行交互。  

本主题将解释如何将外部（"out of tree"）模块添加到PX4构建系统中。  

:::tip  
我们鼓励您尽可能将修改贡献回PX4！  
:::  

## 使用方法  

要创建外部模块：  

- 创建一个**外部目录**文件夹用于分组外部模块：  
  - 该目录可以位于**PX4-Autopilot**树的任何外部位置。  
  - 必须与**PX4-Autopilot**具有相同的目录结构（即必须包含名为**src**的目录）。  
  - 后续我们将其称为`EXTERNAL_MODULES_LOCATION`。  
- 将现有模块（例如**examples/px4_simple_app**）复制到外部目录，或直接创建新模块。  
- 重命名模块（包括**CMakeLists.txt**中的`MODULE`）或从现有的PX4-Autopilot **cmake**构建配置中移除它。  
  这是为了避免与内部模块冲突。  
- 在外部目录中添加一个**CMakeLists.txt**文件，内容如下：  

  ```cmake
  set(config_module_list_external
      modules/<new_module>
      PARENT_SCOPE
      )
  ```

- 在`px4_add_module()`中，向`modules/<new_module>/CMakeLists.txt`添加一行`EXTERNAL`，例如：  

  ```cmake
  px4_add_module(
  	MODULE modules__test_app
  	MAIN test_app
  	STACK_MAIN 2000
  	SRCS
  		px4_simple_app.c
  	DEPENDS
  		platforms__common
  	EXTERNAL
  	)
  ```

## 树外（Out-of-Tree）uORB消息定义  

uORB消息也可以在树外定义。为此，必须存在`$EXTERNAL_MODULES_LOCATION/msg`目录。  

- 将所有新消息定义放在`$EXTERNAL_MODULES_LOCATION/msg`目录中。  
  这些新树外消息定义的格式与任何其他[uORB消息定义](../middleware/uorb.md#adding-a-new-topic)相同。  
- 添加一个文件`$EXTERNAL_MODULES_LOCATION/msg/CMakeLists.txt`，内容如下：  

  ```cmake
  set(config_msg_list_external
      <message1>.msg
      <message2>.msg
      <message3>.msg
      PARENT_SCOPE
      )
  ```

  其中`<message#>.msg`是要处理并用于uORB消息生成的uORB消息定义文件名称。  

树外uORB消息将生成在与普通uORB消息相同的位置。  
uORB主题头文件生成在`<build_dir>/uORB/topics/`目录中，消息源文件生成在`<build_dir>/msg/topics_sources/`目录中。  

新uORB消息的使用方式与任何其他uORB消息相同，如[此处](../middleware/uorb.md#adding-a-new-topic)所述。  

:::warning  
树外uORB消息定义的名称不能与任何普通uORB消息相同。  
:::  

## 构建外部模块和uORB消息  

执行 `make px4_sitl EXTERNAL_MODULES_LOCATION=<path>`。  

可以使用任何其他构建目标，但构建目录必须尚未存在。  
如果已存在，也可以直接在构建文件夹中设置_cmake_变量。  

对于后续增量构建，不需要指定`EXTERNAL_MODULES_LOCATION`。