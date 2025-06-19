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
