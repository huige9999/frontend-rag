# Git基本概念

## 什么是Git？

Git是一个分布式版本控制系统，用于跟踪代码变化、协作开发和版本管理。它由Linus Torvalds于2005年创建，现在是世界上最流行的版本控制系统。

### 核心特点

- **分布式**：每个开发者都有完整的代码仓库副本
- **高效**：采用快照流的方式存储数据，性能优越
- **可靠**：数据完整性校验，确保内容不被损坏
- **灵活**：支持多种工作流程

## Git基本概念

### 1. 仓库（Repository）

仓库是Git管理项目文件的地方，包含所有文件、历史记录和配置信息。

```shell
# 在当前目录新建一个Git代码库
$ git init

# 新建一个目录，将其初始化为Git代码库
$ git init [project-name]

# 下载一个项目和它的整个代码历史
$ git clone [url]
```

### 2. 工作区、暂存区和本地仓库

Git有三个主要的工作区域：

```
工作区 → 暂存区 → 本地仓库 → 远程仓库
```

#### 工作区（Working Directory）
- 你实际操作的文件目录
- 可以查看和编辑文件

#### 暂存区（Staging Area/Index）
- 临时存储将要提交的文件
- 通过`git add`命令将文件添加到暂存区

```shell
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
$ git add [dir]

# 添加当前目录的所有文件到暂存区
$ git add .

# 添加每个变化前，都会要求确认
$ git add -p
```

#### 本地仓库（Local Repository）
- 存储项目的历史记录
- 通过`git commit`命令将暂存区内容提交到仓库

```shell
# 提交暂存区到仓库区
$ git commit -m "提交说明"

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m "新的提交说明"

# 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

### 3. 分支（Branch）

分支是Git的强大功能之一，允许并行开发而不影响主线。

```shell
# 列出所有本地分支
$ git branch

# 列出所有远程分支
$ git branch -r

# 列出所有本地分支和远程分支
$ git branch -a

# 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

# 新建一个分支，并切换到该分支
$ git checkout -b [branch]

# 或者使用新命令（推荐）
$ git switch -c [branch]

# 新建一个分支，指向指定commit
$ git branch [branch] [commit]

# 切换到指定分支，并更新工作区
$ git checkout [branch-name]

# 或者使用新命令（推荐）
$ git switch [branch-name]

# 切换到上一个分支
$ git checkout -

# 合并指定分支到当前分支
$ git merge [branch]

# 删除分支
$ git branch -d [branch-name]

# 删除远程分支
$ git push origin --delete [branch-name]
```

### 4. 远程仓库（Remote）

远程仓库是托管在网络上的Git仓库，用于团队协作。

```shell
# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

# 下载远程仓库的所有变动
$ git fetch [remote]

# 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

# 允许不相关历史提交,并强制合并
$ git pull origin master --allow-unrelated-histories

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

# 推送所有分支到远程仓库
$ git push [remote] --all
```

## Git配置

### 配置用户信息

首次使用Git时，需要配置用户信息：

```shell
# 配置用户名
$ git config --global user.name "Your Name"

# 配置邮箱
$ git config --global user.email "email@example.com"

# 查看配置
$ git config --list
```

### 创建SSH Key

用于与远程仓库（如GitHub）进行安全通信：

```shell
# 生成SSH密钥
$ ssh-keygen -t rsa -C "youremail@example.com"

# 查看公钥内容
$ cat ~/.ssh/id_rsa.pub
```

## 查看信息

### 查看状态和日志

```shell
# 显示有变更的文件
$ git status

# 显示当前分支的版本历史
$ git log

# 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

# 搜索提交历史，根据关键词
$ git log -S [keyword]

# 显示过去5次提交
$ git log -5 --pretty --oneline

# 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]

# 显示指定文件相关的每一次diff
$ git log -p [file]
```

### 查看差异

```shell
# 显示暂存区和工作区的差异
$ git diff

# 显示暂存区和上一个commit的差异
$ git diff --cached [file]

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

# 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

# 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"
```

## 文件管理

### 删除和重命名文件

```shell
# 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

# 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

## 撤销操作

### 恢复文件

```shell
# 恢复暂存区的指定文件到工作区
$ git checkout [file]

# 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

# 恢复暂存区的所有文件到工作区
$ git checkout .
```

### 重置操作

```shell
# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区
$ git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]
```

### 其他撤销操作

```shell
# 新建一个commit，用来撤销指定commit
# 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

# 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

## 标签管理

标签用于标记重要的版本节点。

```shell
# 列出所有tag
$ git tag

# 新建一个tag在当前commit
$ git tag [tag]

# 新建一个tag在指定commit
$ git tag [tag] [commit]

# 删除本地tag
$ git tag -d [tag]

# 删除远程tag
$ git push origin :refs/tags/[tagName]

# 查看tag信息
$ git show [tag]

# 提交指定tag
$ git push [remote] [tag]

# 提交所有tag
$ git push [remote] --tags

# 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

## .gitignore文件

`.gitignore`文件用于指定Git需要忽略的文件或目录。

### 配置语法

- 以斜杠`/`开头表示目录
- 以星号`*`通配多个字符
- 以问号`?`通配单个字符
- 以方括号`[]`包含单个字符的匹配列表
- 以叹号`!`表示不忽略(跟踪)匹配到的文件或目录

### 配置示例

```gitignore
# 忽略目录 fd1 下的全部内容
fd1/*

# 忽略根目录下的 /fd1/ 目录的全部内容
/fd1/*

# 忽略全部内容，但是不忽略 .gitignore 文件、根目录下的 /fw/bin/ 和 /fw/sf/ 目录
/*
!.gitignore
!/fw/bin/
!/fw/sf/

# 常见忽略配置
node_modules/
dist/
.env
*.log
.DS_Store
```

## Git工作流程

### 基本工作流程

1. **修改文件**：在工作区中修改文件
2. **添加到暂存区**：使用`git add`将修改添加到暂存区
3. **提交到仓库**：使用`git commit`将暂存区内容提交到本地仓库
4. **推送到远程**：使用`git push`将本地提交推送到远程仓库

### 分支工作流程

1. **创建分支**：从主分支创建新分支进行开发
2. **开发提交**：在新分支上进行开发和提交
3. **合并分支**：将完成的分支合并回主分支
4. **删除分支**：删除已完成的开发分支

## 常见问题

### 如何撤销未提交的修改？

```shell
# 撤销工作区的修改
$ git checkout -- [file]

# 撤销暂存区的修改
$ git reset HEAD [file]
```

### 如何查看某次提交的详细信息？

```shell
# 显示某次提交的元数据和内容变化
$ git show [commit]

# 显示某次提交发生变化的文件
$ git show --name-only [commit]

# 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]
```

### 如何查看文件的修改历史？

```shell
# 显示指定文件是什么人在什么时间修改过
$ git blame [file]
```

## 总结

Git基本概念包括：
- **仓库**：存储项目文件和历史记录
- **工作区**：实际操作的文件目录
- **暂存区**：临时存储将要提交的文件
- **分支**：并行开发的独立线
- **远程仓库**：网络上的Git仓库

掌握这些基本概念是学习Git的基础，后续章节将深入学习Git的高级用法和工作流程。
