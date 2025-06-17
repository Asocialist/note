# Cmake

- Cmake 是开源的跨平台自动化构建系统 管理软件建制的程序，不依赖特定编译器支持多层目录多个应用程序，多个函数库
- 简化配置 通过CmakeList.txt文件来定义项目结构 依赖和编译项

## 构建配置

- CmakeList.txt 配置项目
- 构建目录

## 构建流程

1. 编写CmakeList.txt
2. 生成构筑文件 CMake
3. 执行构筑

## CmakeList.txt文件

- 配置文件由于定义项目的构建规则以来关系 编译项
- 基本语法

  1. 指定cmake最低版本 `cmake_minimum_required(VERSION <version>)`
  2. 定义项目名称和使用的编程语言`project(<project_name>[<language>....])`
  3. 指定要生成的可执行文件和其源文件`add_excutable(MyExcutable main.cpp other_file.cpp)`
  4. 创建库及其源文件`add_library(<target> <source_files>...)`
  5. 链接目标文件与其他库`target_link_libraries(<target> <libraries>...)`
  6. include_direcories(${PROJECT_SOURCE_DIR}/include)
  7. 设置变量的值`set(<variable> <value>...)`
  8. 设置目标属性:`target_include_directories(TARGET target_name [BEFORE | AFTER] [SySTEM] [PUBLIC | PRIVATE INTERFACE] [items1...])`
  9. 条件语句（if ,elseif,else,endif）

        ```cmake
        if(expression)
        #commands
        elseif(expression)
        #commands
        endif()
        ```

  10. 自定义命令(add_custom_command)

        ```cmake
        add_custom_command(
        TARGET target
        PRE_BUILD | PRE_LINK | POST_BUILD
        COMMAND command1 [ARGS] [WORKING_DIRECTORY dir]
        [COMMAND command2 [ARGS]]
        [DEPENDS [depend1 [depend2 ...]]]
        [COMMENT comment]
        [VERBATIM]
        )
        ```

## 变量和缓存

- 定义变量：`set(MY_VAR "HelloWorld")`
- 使用: `message(STATUS "Variable MY_VAR is ${MY_VAR}")`
