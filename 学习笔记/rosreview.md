# ROS review

- 复习ros用于进行研究和学习机器人使用
- node topic service的构建方法 cpp 和 python两种
- 来源ros wiki 和中文网站等

## ros file

1. rospack 获取软件包的有关信息 `rospack find [package_name]`
2. `roscd [locationname]` 切换包位置
3. `rosls [locationname]`ls

## ros make workspace

- `mkdir -p catkin_ws/src`
- 在catkin_ws下执行 `catkin_init_workspace`
- 创建完成后通过`catkin_make`编译

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
  - 会创建一个packagename的文件夹中包含 package.xml和CMAKEList.txt 部分填写了你在执行catkin_create_pkg命令时提供的信息

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

    - 特殊类型Header案由时间戳和ros中广泛使用的坐标帧信息一般在msg文件第一行看到Header header
    - srv文件与msg文件一样知识包含两个部分请求和相应通过---线隔开

    ```srv
    int64 A
    int64 B
    ---
    int64 Sum
    ```

    - A和B是请求 Sum是响应

 1. 使用msg

    - 创建一个filename.msg元素每行一个
    - 需要确保能够转换为C++ python等其他语言的source code
    - 打开package.xml确保由一下内容

    ```xml
    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>
    ```

    - 打开CMakeLists.txt 在find_package()中添加message_generation依赖项
    - 确保导出消息运行时的依赖

    ```txt
    catkin_package(
      CATKIN_DEPENDS message_runtime ...
    )
    ```

    ```txt
    add_message_files(
      FILES
      filename.msg
    )
    ```

    - 确保这部分不是注释。手动添加.msg之后要确保Cmake合适需要重新配置项目。确保generate_message()函数被调用

    ```txt
    generate_messages(
      DEPENDENCIES
      std_msgs
    )
    ```

    - 使用rosmsg
      - `rosmsg show [message type]`来查看ros是否能够识别
      - message type 由定义消息的软件包 和消息名称组成

 1. 使用srv

    - 创建srv 从另一个包赋值现有的srv定义 `mkdir srv`
    - `roscp [packagename] [file_to_copy_path] [copy_path]` 同样需要确保可以被转换成别的语言的源码
    - 同样需要在package.xml中修改服务字段

    ```xml
    add_service_files(
      FILES
      filename.srv
    )
    ```

 1. ros工具获取帮助 `rosmsg -h` `rosmsg show -h`

## ros创建node cpp实现

- `catkin_create_pkg my_robot_driver roscpp std_msgs`建立基础环境
- my_robot_driver是包的名称 std_msgs等是要用到的依赖
- 先编写package.xml 注意包的基本信息和依赖关系

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>node_review</name>
  <version>0.1.0</version>
  <description>The my_awesome_package description</description>

  <maintainer email="email">name</maintainer>

  <license>Apache-2.0</license>

  <buildtool_depend>catkin</buildtool_depend>

  <build_depend>roscpp</build_depend>
  <build_depend>std_msgs</build_depend>
  <build_depend>sensor_msgs</build_depend>

  <build_export_depend>roscpp</build_export_depend>
  <build_export_depend>std_msgs</build_export_depend>
  
  <exec_depend>roscpp</exec_depend>
  <exec_depend>std_msgs</exec_depend>
  <exec_depend>sensor_msgs</exec_depend>

  </package>
```

- 编写CmakeLists.txt

  - 检查`find_package`确保含有在xml文件里声明的ros依赖
  - 注意`catkin_package`里CATKIN_DEPENDS与find_package列表一致
  - 声明编译目标

  ``` txt
  ## Build
  add_executable(motor_controller src/motor_controller.cpp)
  ##定义了一个名为 motor_controller 的可执行文件（你的节点），它由 src/motor_controller.cpp 编译而来。
  ## Link libraries
  target_link_libraries(motor_controller
    ${catkin_LIBRARIES}
  )
  ##告诉编译器，motor_controller 这个程序需要链接 ROS 的核心库 (${catkin_LIBRARIES}) 才能运行。
  ```

  - 自定义消息

    - 在package.xml下添加依赖

    ```xml
    <build_depend>message_generation</build_depend>
    <exec_depend>message_runtime</exec_depend>
    ```

  - 在CmakeLists.txt中
  
    - 更新find_package 添加message_generation
    - 在catkin_package之前

      ```txt
      add_message_files(FILES MotorCommand.msg)
      generate_messages(DEPENDENCIES std_msgs)
      ```

    - 更新catkin_packge添加`CATKIN_DEPENDS roscpp std_msgs message_runtime`
    - 为节点添加编译顺序依赖

     ```txt
     add_executable(motor_controller src/motor_controller.cpp)

     # 告诉编译器，必须先生成消息头文件，再编译这个节点
     add_dependencies(motor_controller ${PROJECT_NAME}_generate_messages_cpp)

     target_link_libraries(motor_controller
     ${catkin_LIBRARIES}
    )
     ```

```txt
cmake_minimum_required(VERSION 3.0.2)
project(node_review)

# 强制使用 C++14 标准 (ROS Noetic 推荐)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 查找所有依赖的包，这是现代且推荐的方式
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  message_generation
)

# 声明 Catkin 包信息 (用于生成消息、服务等)
## Declare ROS messages and services
# add_message_files(FILES Num.msg)
# add_service_files(FILES AddTwoInts.srv)

# 1. 声明你的消息和服务文件
# add_message_files(FILES
#   Num.msg
# )

# add_service_files(FILES
#   AddTwoInts.srv
# )
# 2. 调用此命令来实际生成代码
# generate_messages(
#   DEPENDENCIES
#     std_msgs # 如果你的.msg/.srv文件内部使用了std_msgs类型
# )
# 3. 为你的包“贴上标签”，声明依赖关系
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES my_awesome_library
  CATKIN_DEPENDS roscpp std_msgs
#  DEPENDS system_lib
)


###########
#  构建   #
###########

# 指定头文件目录
# 这使得你在代码中可以直接 #include "my_awesome_package/my_header.h"
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

## ------------------ 构建可执行文件 (节点) ------------------
# 为目标链接 Catkin 库
add_executable(talker src/talker.cpp)
target_link_libraries(talker ${catkin_LIBRARIES})
# add_dependencies(talker ${PROJECT_NAME}_generate_messages_cpp)
add_executable(listener src/listener.cpp)
target_link_libraries(listener ${catkin_LIBRARIES})
# add_dependencies(listener ${PROJECT_NAME}_generate_messages_cpp)

## ------------------ 构建库 (可选) ------------------
# add_library(my_awesome_library src/my_library.cpp)
#
# # 为目标链接 Catkin 库
# target_link_libraries(my_awesome_library
#   ${catkin_LIBRARIES}
# )

############
#  安装   #
############
# install(...)
```

- C++ 实现 publisher和subscriber

```C++
//publisher
#include "ros/ros.h"
#include "std_msgs/String.h"

#include <sstream>

/**
 * This tutorial demonstrates simple sending of messages over the ROS system.
 */
int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "talker"); //name node

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;

  /**
   * The advertise() function is how you tell ROS that you want to
   * publish on a given topic name. This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing. After this advertise() call is made, the master
   * node will notify anyone who is trying to subscribe to this topic name,
   * and they will in turn negotiate a peer-to-peer connection with this
   * node.  advertise() returns a Publisher object which allows you to
   * publish messages on that topic through a call to publish().  Once
   * all copies of the returned Publisher object are destroyed, the topic
   * will be automatically unadvertised.
   *
   * The second parameter to advertise() is the size of the message queue
   * used for publishing messages.  If messages are published more quickly
   * than we can send them, the number here specifies how many messages to
   * buffer up before throwing some away.
   */
  ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);

  ros::Rate loop_rate(10);

  /**
   * A count of how many messages we have sent. This is used to create
   * a unique string for each message.
   */
  int count = 0;
  while (ros::ok())
  {
    /**
     * This is a message object. You stuff it with data, and then publish it.
     */
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();

    ROS_INFO("%s", msg.data.c_str());

    /**
     * The publish() function is how you send messages. The parameter
     * is the message object. The type of this object must agree with the type
     * given as a template parameter to the advertise<>() call, as was done
     * in the constructor above.
     */
    chatter_pub.publish(msg);

    ros::spinOnce();

    loop_rate.sleep();
    ++count;
  }


  return 0;
}
```

- 代码讲解:

  - `#include "ros/ros.h"` `#include "std_msgs/String.h"` 包含库调用ros信息和ros库
  - `ros::init(argc, argv, "talker");`argc (argument count) 和 argv (argument vector) 是 C++ main 函数的标准参数，它们负责接收从命令行传递给程序的参数。为了让 ROS 能够处理它自己特有的命令行参数为了重映射 命名自己的节点
  - `ros::NodeHandle n;`为这个进程的节点创建句柄。创建的第一个NodeHandle实际上将执行节点的初始化，而最后一个被销毁的NodeHandle将清除节点所使用的任何资源。
  - `ros::Publisher chatter_pub = n.advertise<std_msgs::String>("chatter", 1000);` 调用ros下Publisher对象 对象名为chatter_pub n是上面初始化的句柄 chatter是话题名 模板是std_msgs::String在这里修改发布信息的类型 通过advertise的方式广播
  - `ros::Rate loop_rate(10);`设置循环频率

  ```C++
    std_msgs::String msg;

    std::stringstream ss;
    ss << "hello world " << count;
    msg.data = ss.str();
  ```

  - 使用一种消息自适应的类在ROS上广播消息，该类通常由msg文件生成。更复杂的数据类型也可以，不过我们现在将使用标准的String消息，它有一个成员：data
  - `chatter_pub.publish(msg);`发布信息
  - `ROS_INFO("%s", msg.data.c_str());` 取代printf和cout
  - `ros::spinOnce();` 接受回调
  - `loop_rate.sleep();`在剩余时间内睡眠以保持频率

```C++
//subscriber
#include "ros/ros.h"
#include "std_msgs/String.h"

/**
 * This tutorial demonstrates simple receipt of messages over the ROS system.
 */
void chatterCallback(const std_msgs::String::ConstPtr& msg)
{
  ROS_INFO("I heard: [%s]", msg->data.c_str());
}

int main(int argc, char **argv)
{
  /**
   * The ros::init() function needs to see argc and argv so that it can perform
   * any ROS arguments and name remapping that were provided at the command line.
   * For programmatic remappings you can use a different version of init() which takes
   * remappings directly, but for most command-line programs, passing argc and argv is
   * the easiest way to do it.  The third argument to init() is the name of the node.
   *
   * You must call one of the versions of ros::init() before using any other
   * part of the ROS system.
   */
  ros::init(argc, argv, "listener");

  /**
   * NodeHandle is the main access point to communications with the ROS system.
   * The first NodeHandle constructed will fully initialize this node, and the last
   * NodeHandle destructed will close down the node.
   */
  ros::NodeHandle n;

  /**
   * The subscribe() call is how you tell ROS that you want to receive messages
   * on a given topic.  This invokes a call to the ROS
   * master node, which keeps a registry of who is publishing and who
   * is subscribing.  Messages are passed to a callback function, here
   * called chatterCallback.  subscribe() returns a Subscriber object that you
   * must hold on to until you want to unsubscribe.  When all copies of the Subscriber
   * object go out of scope, this callback will automatically be unsubscribed from
   * this topic.
   *
   * The second parameter to the subscribe() function is the size of the message
   * queue.  If messages are arriving faster than they are being processed, this
   * is the number of messages that will be buffered up before beginning to throw
   * away the oldest ones.
   */
  ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);

  /**
   * ros::spin() will enter a loop, pumping callbacks.  With this version, all
   * callbacks will be called from within this thread (the main one).  ros::spin()
   * will exit when Ctrl-C is pressed, or the node is shutdown by the master.
   */
  ros::spin();

  return 0;
}
```

- chatterCallback函数是一个回调函数当有新消息到达chatter话题时它就会被调用。
- `ros::Subscriber sub = n.subscribe("chatter", 1000, chatterCallback);`跟publisher一样建立一个subcriber 对象名为sub订阅chatter话题
- `ros::spin();`启动一个自循环来不断调用回调函数

## ros创建node python实现

- 同样建立一个publisher节点和一个subscriber节点
- 代码要放在/packagename/script下 注意权限的添加
- 修改CmakeLists.txt

```txt
catkin_install_python(
  PROGRAMS
    scripts/talker.py
    scripts/listener.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```

- 将以上代码放入到CmakeLists.txt 这一步是在/bin下安装对应脚本

``` python
#!/usr/bin/env python
# license removed for brevity
import rospy
from std_msgs.msg import String

def talker():
    pub = rospy.Publisher('chatter', String, queue_size=10)
    rospy.init_node('talker', anonymous=True)
    rate = rospy.Rate(10) # 10hz
    while not rospy.is_shutdown():
        hello_str = "hello world %s" % rospy.get_time()
        rospy.loginfo(hello_str)
        pub.publish(hello_str)
        rate.sleep()

if __name__ == '__main__':
    try:
        talker()
    except rospy.ROSInterruptException:
        pass
```

- 代码解释：

  - `#!/usr/bin/env python` 确保脚本按照Python脚本执行
  - `import rospy` `from std_msgs.msg import String`  初始化调用String信息类作为字符串容器发布
  - `pub = rospy.Publisher('chatter', String, queue_size=10)`定义了publisher对象和话题名String是std_msgs.String类queue_size是为了限制排队的消息数量
  - `rospy.init_node('talker', anonymous=True)` 规定节点名称 anonymous=True是让名称末尾添加随机数确保节点具有唯一名称
  - `rate = rospy.Rate(10) # 10hz` 使用sleep方法按照规定速率循环
  - `while not rospy.is_shutdown():`部分 标准的rospy结构 注意`rospy.loginfo(str)`会将消息打印到屏幕上 将消息写入节点的日志文件 写入rosout(可以通过rqt_console拉取信息查看相关的输出) `pub.publish(str)`广播信息
  - 发布复杂的消息类型方法
    - 先引入复杂消息类型 造函数参数的顺序与.msg文件中的顺序相同。也可以不传入任何参数，直接初始化字段`msg = String()` `msg.data = str`
    - 自定义复杂消息在msg/filename.msg中定义然后在Cmakelists和package.xml文件中声明消息依赖 编译后使用

  - `try:` 的部分 除了标准的Python __main__检查，它还会捕获一个rospy.ROSInterruptException异常，当按下Ctrl+C或节点因其他原因关闭时，这一异常就会被rospy.sleep()和rospy.Rate.sleep()抛出。引发此异常的原因是你不会意外地在sleep()之后继续执行代码。

``` python
#!/usr/bin/env python
import rospy
from std_msgs.msg import String

def callback(data):
    rospy.loginfo(rospy.get_caller_id() + "I heard %s", data.data)
    
def listener():

    # In ROS, nodes are uniquely named. If two nodes with the same
    # name are launched, the previous one is kicked off. The
    # anonymous=True flag means that rospy will choose a unique
    # name for our 'listener' node so that multiple listeners can
    # run simultaneously.
    rospy.init_node('listener', anonymous=True)

    rospy.Subscriber("chatter", String, callback)

    # spin() simply keeps python from exiting until this node is stopped
    rospy.spin()

if __name__ == '__main__':
    listener()
```

- 解释:

  - `rospy.spin()` 引入新的回调机制与roscpp不同rospy.spin()不影响订阅者回调函数有自己的线程
- 记得`source ~/workspacename/devel/setup.bash`
