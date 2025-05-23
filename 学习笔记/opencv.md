# Open CV 笔记

- open cv 介绍： 计算机视觉库
- 为什么学习： 研究需要，计算机图像处理能力提升
- 学习目标： 掌握常用Mat类，使用方法及图像处理算法  

-------

1. *Mat类*

    - Mat类 opencv中储存和处理图像主要数据结构
    - 复制Mat类对象应使用Mat::clone（）函数
    - `Mat(nrows, ncols, type, fillValue)`构造器创建Mat类  
  
    - 原始类型定义

        ```` (C++)  
        CV_<bit depth>{U|S|F}C(<number of channels>)
        ````  

    - openCV按照BGR顺序保存RGB图形  
    - 包含头文件后应添加 `using namespace cv`  

2. *窗口设置*  
