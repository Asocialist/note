# ROS review

- 复习ros用于进行研究和学习机器人使用
- node topic service的构建方法 cpp 和 python两种
- 来源ros wiki 和中文网站等

## ros file

1. rospack 获取软件包的有关信息 `rospack find [package_name]`
2. `roscd [locationname]` 切换包位置
3. `rosls [locationname]`ls

## ros make package

- catkin package 要满足：
  
  - 符合catkin规范的package.xml
  - 符合catkin规范的CmakeList.txt
  - 有自己的目录 再同一个目录下不能有嵌套或多个软件包存在

- catkin work space

    ```bash
    mkdir -p ~/catkin_ws/src
    cd ~/catkin_ws/
    catkin build
    ```

- 创建软件包

  - 使用`catkin_create_pkg <packagename> [depend1] [depend2] [depend3]`来创建软件包
  - 会创建一个packagename的文件夹中包含 package.xml和CM艾克List.txt 部分填写了你在执行catkin_create_pkg命令时提供的信息

- 在catkin工作区生效配置文件`cd ~/catkin_ws` `catkin build`
- 软件包依赖

  1. 一级依赖

     - 使用`catkin_create_pkg`时提供了软件包作为依赖关系 通过rospack来检查
     - `rospack depends1 <packagename>`
  
  1. 间接依赖

     - 很多情况下一个依赖包会有自己的依赖`rospack depends <packagename>`可以检测出所有嵌套依赖

  1. 自定义软件包

     1. 自定义package.xml

         - description标签: `<description> </description>` 软件包的描述信息 随便
         - maintainer标签`<maintainer email =""> user </maintainer>`联系软件包相关人员
         - license标签`<license> </license>`
         - 依赖项标签 分为build_depend,buildtool_depend,run_depend,test_depend `<builtool_depend>catkin</buildtool_depend>`默认提供 注意运行和编译时需要的包
     1. 自定义CMakeLists.txt

## ros计算图

- 计算图由ros进程组成的点对点网络通过Nodes, Topics,   Master, Parameter Server, Messages, Bags, 向Graph提供数据

  - Nodes：可执行文件 最小单元可以发布和订阅 提供和使用服务
  - 客户端库 实现了不同编程语言编写的节点进行相互通信

    - rospy roscpp
  
  - roscore 在运行所有ros程序前首先要运行的命令`roscore`
  - rosnode `rosnode list`
  - `rosrun [packagename] [nodename]`直接运行软件包内的节点不需要知道路径
  - Topics 节点间通过topic通信 `rosrun rqt_graph rqt_graph`产看现在的topic
  - `rostopic -h`获取可用的rostopic的子命令
  - `rostopic echo [topic]`查看节点上发布的信息数据
  - `rostopic list`查看现在被订阅和发布的所有话题
  - Ros消息 发布者和订阅者必须发布和接受相同类型的消息 `rostopic type [topic]`查看所发布话题的类型
  - `rostopic pub [topic] [msg_type] [args]`将数据发布到当前正在广播的话题上 注意消息使用的是什么语法 大多为yaml
  - 数据发布的速率 `rostopic hz`
  - rqt_plot在滚动时间图上显示发布在某个话题上的速率`rosrun rqt_plot rqt_plot`

## Ros服务和参数

- service是node间通讯的另一种方式 允许node发送request和获取response
- 使用rosservice

  - rosservice服务命令

    ```ros
    rosservice list
    rosservice call 用给定的参数调用服务
    rosservice type 输出服务的类型
    rosservice find 按服务的类型查找
    rosservice uri 输出服务的ROSRPC uri
    ```

- 使用rosparam

  - rosparam使我们让在ros参数服务其上储存和操作数据。服务器能储存int，float，boolean，dictionaries，lilst等数据类型。通过YAML标记语言的语法。

    ```rosbash
    rosparam set 设置参数
    rosparam get 获取参数
    rosparam load 从文件中加载参数
    rosparam duump 向文件中转储参数
    rosparam delete 删除参数
    rosparam list 
    ```

  - `rosparam dump [filename] [namespace]` `rosparam load [filename] [namespace]` 可以将参数写入新的yaml文件并可以将yaml文件重新载入到新的命名空间

## rqt_console 和roslaunch

- ros_console 连接到了ros的日志框架 以显示节点输出的信息 rqt_logger_level允许我们在节点运行时改变输出详细信息主要看错误`rosrun rqt_console rqt_console` `rosrun rqt_logger_level rqt_logger_level`
- `roslaunch [package] [filename.launch]`启动定义在launch文件中的节点
- launch 文件 filename.launch是一个xml类文件

    ```xml
    <launch>

    <group ns="">
    <node pkg ="" name="" type=""/>
    </group>
    </launch>
    ```

  - 建立分组通过namespace区分 定义node 通过`roslaunch packagename filename.launch` 启动

## rosed

- rosed rosbash的一部分 利用他直接通过软件包名编辑包中的文件无需键入完整路径`rosed [packagename] [filename]`
- 默认编辑器是vim

## 创建ros消息和服务

 1. msg和srv

    - msg: 文本文件 描述ros消息的字段为不同编程语言编写的消息生成源代码
    - srv: 一个srv文件描述一个服务 由reruest和response组成
    - msg在软件包的msg目录下 srv 在srv目录下
    - msg可用类型：
  
      - int float string time duration 其他msg
      - variable-length array[] fixed-length array[C]
