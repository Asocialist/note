# Git 复习

- git是代码仓库用于代码版本管理

- 复习git用于进行研究代码的管理和实习的需要

## 基本操作

- 建立仓库 `git init`

- 查看仓库 `git status`

- 添加文件 `git add`

- 保存仓库记录 `git commit -m ""`
- 上传master分支上所有内容`git push -u respositories master`
- 查看日志 `git log`

  - `git log -p` 查看提交前后差异

- 查看工作树与暂存区的区别 `git diff`

  - `git commit`之后`git diff`

## 分支 多并行作业

多个并行作业使用

- 显示分支 `git branch`

- *显示的是当前分支

- 特性分支 集中实现单个特性，除此之外不进行别的分支作业

- 合并分支 `git merge`

  - 在master上输入合并命令 并且加上 --no--ff参数
  `git merge --no--ff feature`

  - 保存编辑器中内容并关闭

- 图表显示日志分支 `git log --graph`

## 更改提交的操作

- 回溯版本 `git reset`
  
  1. `git rest --hard` 提供目标时间点的哈希值
  2. `git reflog`查看操作时间哈希值
  3. 消除冲突
     在master上`git merge --no--ff feature`在编辑器中修改并保存

  4. 提交 执行`git add` `git commit`
     `git commit --amend`来启动编辑器修改提交信息

- 压缩历史 `git rebase -i`
