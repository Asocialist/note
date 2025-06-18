# linux ros command review

## 学习目标

    - 掌握linux操作和ros操作用于研究和开发软件包
    - shell是一种特殊的交互式工具。
    - 它为用户提供了启动程序、管理文件系统中的文件以及运行在Linux系统上的进程的途径。
    - shell的核心是命令行提示符。命令行提示符是shell负责交互的部分。它允许你输入文本命令，然后解释命令，并在内核中执行。

### 文件操作

- `touch` make file
- `ls -l`list by condition
- `ls -F -R`list hidden file
- `cp -i`copy file
- `rm -rf directory` 删除文件夹及其所有内容
- `file my_file`check the file type
- `cat -n file`查看文件内容并显示行号
- `more file` 显示每页内容后停下

### bash shell

- `ps -p pidlist` 查看进程 `ps -A`
- `top`实时显示进程
- `kill PID`关闭进程
- `du -c` `du-h`显示指定文件大小
- `sort` 对数据进行排序通过文本的方式 排序数字需要`sort -n`
- `grep [options] pattern [file]` 查找匹配指定模式字符的行 `man grep`查看grep手册
- `gzip` 用来压缩文件 `gzcat`查看已压缩的文件 `gunzip`解压缩
- `tar function [options] object1 object2` function参数定义了tar命令该做什么  `tar -zxvf filename.tgz`解压

### shell process

- bash shell 是一个程序 运行时成为一个进程
- 进程列表 命令分组的一种会创建子shell `(pwd ; ls ; cd /etc; ls)`
- bash间有父子关系
- 你可以在一行中指定要依次运行的一系列命令。这可以通过命令列表来实现只需要在命令S之间加入分号（;）即可。
- `echo $BASH_SUBSHELL`查看子bash
- `sleep` 进程等待秒数 在指令后加`&`使进程进入后台模式
- 后台模式：

  - 在后台模式运行命令可以在处理命令时让出CLI 进行别的处理
  - `jobs`显示后台作业信息
  - 将进程列表放入后台 进程列表是运行在子shell中的一条或多条命令。 `(sleep 2; echo $BASH_SUBSHELL ; sleep2)&`
  - 协程 可以同时做两件事 在后台生成一个子shell并在这个子shell中执行命令`coporc MY_Job { sleep 10; }`携程可以自己设置名字 可以与进程列表结合产生嵌套的子shell 不推荐
- 内建命令

  - 外部命令 存在与bash shell 之外的程序 位于/bin /usr/bin 等
    - 外部命令执行时会创建子进程 称作衍生
    - 注:即使衍生出子进程或子shell仍可以通过发送信号与其沟通
  - 内建命令 不需要子进程来执行。和shell编译成议题 作为shell工具的组成部分 通过`type`命令来查看
    - `history`查看最近用过的命令列表 多个终端会话可以使用`history -a`在打开的会话中向.bash_history文件添加记录想要更新所有的会话记录使用 `history -n`
    - `alias`命令别名 为常用的命令创建另一个名称
- Linux环境变量

  - 定义：bash shell储存有关shell会话和工作环境的信息的一个特性。允许在内存中储存数据便于程序或shell中运行的脚本能够轻松访问到

    - 全局变量与局部变量

      - 对于shell会话和所有子shell都是可见的。局部变量只对创建他们的shell可见
      - 没有只显示局部环境变量的命令 `set`命令会显示为某个特定进程设置的所有环境变量

    - 设置自定义变量

      - 通过等号给环境变量赋值可以为数字或字符串
      - `echo $my_variable` `my_variable=Hello`如果要给变量赋一个含有空格的字符串用单引号好界定字符串的首尾`my_variable="Hello World"`生成另一个shell在子shell中就不可用了
      - 设置全局环境变量

        - 先创建一个局部环境变量再导出道全局环境变量`export`指令实现 `export my_variable`不加$
        - 修改子设立了中全局环境变量的值不影响父shell中的值
      - 删除环境变量

        - `unset my_variable`
        - 如果要用到变量，使用$；如果要操作变量，不使用$。这条规则的一个例外就是使用printenv显示某个变量的值。
      - 设置PATH环境变量

        - `echo $PATH`如果命令或者程序的位置没有包括在PATH变量中，那么如果不使用绝对路径的话，shell是没法找到的。
        - PATH中各个目录通过冒号隔离只需引用原来的PATH值给字符串添加新目录就可以了
        - 注意如果希望子shell也能找到你的程序的位置将修改后的PATH环境变量导出
    - 交互式shell进程

      - 不是登录系统时进入的bash需要指令输入

    - 非交互式shell

      - 系统执行shell脚本时启动没有CLI
    - 环境变量持久化 配置文件在 ~/.bashrc
    - 数组变量 环境变量可以作为数组使用

### linux 安全权限

