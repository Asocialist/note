# Git 复习

- git是代码仓库用于代码版本管理

- 复习git用于进行研究代码的管理和实习的需要

## 基本操作

- 建立仓库 `git init`

- 查看仓库 `git status`

- 添加文件 `git add`

- 保存仓库记录 `git commit -m ""`

- 查看日志 `git log`

  - `git log -p` 查看提交前后差异

- 查看工作树与暂存区的区别 `git diff`

  - `git commit`之后`git diff`

## 分支 多并行作业

多个并行作业使用

- 显示分支 `git branch`

- 创建切换`git checkout -b`

  - `git checkout -` 返回上一个分支

  - *显示的是当前分支

- 特性分支 集中实现单个特性，除此之外不进行别的分支作业

- 合并分支 `git merge`