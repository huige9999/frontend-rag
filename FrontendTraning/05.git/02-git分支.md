# Git分支

## 章节概述

Git分支是Git最强大的功能之一，它允许你从开发主线上分离开来，在不影响主线的同时继续工作。本章节将详细介绍Git分支的概念、操作和最佳实践。

## 学习目标

- 理解Git分支的基本概念和工作原理
- 掌握分支的创建、切换、合并和删除操作
- 了解分支管理策略和最佳实践
- 学会解决分支冲突

## 1. 分支基础

### 1.1 什么是分支

Git中的分支本质上是一个指向某个commit的可移动指针。默认情况下，Git会创建一个名为`master`（或`main`）的主分支。

```bash
# 查看所有分支
git branch

# 查看所有分支（包括远程分支）
git branch -a

# 查看当前分支
git branch --show-current

# 查看分支详细信息
git branch -v
```

### 1.2 分支的工作原理

Git使用一个特殊的指针叫`HEAD`，它指向当前所在的本地分支。创建新分支时，Git只是创建了一个新的指针，但不会改变任何文件内容。

```bash
# HEAD指针示例
# 当你在master分支时
# HEAD -> master -> commit_hash

# 切换到新分支后
# HEAD -> feature -> commit_hash
```

## 2. 分支操作

### 2.1 创建分支

```bash
# 创建新分支（不切换）
git branch feature-login

# 创建并切换到新分支
git checkout -b feature-login
# 或使用新语法
git switch -c feature-login

# 基于指定commit创建分支
git branch feature-login abc1234

# 基于tag创建分支
git branch feature-login v1.0.0
```

### 2.2 切换分支

```bash
# 切换到已存在的分支
git checkout feature-login
# 或使用新语法
git switch feature-login

# 切换到上一个分支
git checkout -
# 或
git switch -

# 强制切换（丢弃未提交的更改）
git checkout -f feature-login
```

### 2.3 查看分支差异

```bash
# 查看分支与当前分支的差异
git diff feature-login

# 查看两个分支之间的差异
git diff master feature-login

# 查看分支合并到当前分支后的预期变化
git diff feature-login...master
```

### 2.4 重命名分支

```bash
# 重命名当前分支
git branch -m new-branch-name

# 重命名指定分支
git branch -m old-branch-name new-branch-name
```

### 2.5 删除分支

```bash
# 删除本地分支（需要先切换到其他分支）
git branch -d feature-login

# 强制删除未合并的分支
git branch -D feature-login

# 删除远程分支
git push origin --delete feature-login
# 或
git push origin :feature-login
```

## 3. 分支合并

### 3.1 合并分支

```bash
# 切换到目标分支（如master）
git checkout master

# 合并其他分支（如feature-login）
git merge feature-login

# 创建合并提交（默认）
git merge --no-ff feature-login

# 快进合并（如果可能）
git merge --ff-only feature-login

# 压缩合并（将多个提交合并为一个）
git merge --squash feature-login
```

### 3.2 合并策略说明

**快进合并（Fast-forward）**：
- 当目标分支是源分支的直接祖先时
- 只是简单地移动指针
- 不会创建合并提交

```bash
# 示例：master在A，feature在B，A是B的祖先
# A <- B (feature)
# 快进后：master和feature都指向B
```

**三方合并（3-way merge）**：
- 当分支有分歧时使用
- 创建新的合并提交
- 需要两个分支的共同祖先

```bash
# 示例：master和feature有分叉
#      C
#    /   \
#  B       D (feature)
#  |       |
#  A       E
#  \      /
#    F (合并提交)
```

### 3.3 处理合并冲突

当两个分支修改了同一文件的同一部分时，Git会报告冲突。

```bash
# 合并时出现冲突
git merge feature-login
# Auto-merging index.html
# CONFLICT (content): Merge conflict in index.html
# Automatic merge failed; fix conflicts and then commit the result.

# 查看冲突文件
git status

# 手动解决冲突后
git add index.html
git commit
```

**冲突标记示例**：

```html
<<<<<<< HEAD
<div class="current">当前分支内容</div>
=======
<div class="incoming">要合并分支的内容</div>
>>>>>>> feature-login
```

**解决冲突的工具**：

```bash
# 使用合并工具
git mergetool

# 中止合并
git merge --abort

# 继续合并（解决冲突后）
git merge --continue
```

## 4. 变基操作（Rebase）

### 4.1 什么是变基

变基是将一系列提交移动到另一个基础commit上的操作，可以创建更线性的提交历史。

```bash
# 将当前分支变基到master
git rebase master

# 将feature分支变基到master（先切换到feature）
git checkout feature
git rebase master
```

### 4.2 变基vs合并

| 特性 | 合并（Merge） | 变基（Rebase） |
|------|--------------|---------------|
| 历史记录 | 保留所有分支历史 | 创建线性历史 |
| 提交数量 | 增加合并提交 | 不增加提交 |
| 复杂性 | 简单安全 | 可能重写历史 |
| 使用场景 | 保留完整历史 | 清理提交历史 |

### 4.3 变基操作示例

```bash
# 交互式变基（修改、删除、重新排序提交）
git rebase -i HEAD~3

# 变基到指定commit
git rebase abc1234

# 继续变基（解决冲突后）
git rebase --continue

# 跳过某个提交
git rebase --skip

# 中止变基
git rebase --abort
```

**注意**：不要对已发布的提交进行变基操作！

## 5. 分支管理策略

### 5.1 Git Flow模型

```bash
# 主要分支
main/master      # 生产环境代码
develop          # 开发环境代码

# 辅助分支
feature/*        # 功能开发分支
release/*        # 发布准备分支
hotfix/*         # 紧急修复分支
```

**工作流程**：

```bash
# 1. 从develop创建功能分支
git checkout develop
git checkout -b feature/user-authentication

# 2. 完成开发后合并回develop
git checkout develop
git merge feature/user-authentication

# 3. 创建发布分支
git checkout -b release/1.0.0 develop

# 4. 发布完成后合并到main和develop
git checkout main
git merge release/1.0.0
git checkout develop
git merge release/1.0.0

# 5. 紧急修复
git checkout -b hotfix/critical-bug main
# 修复后合并到main和develop
```

### 5.2 GitHub Flow模型

```bash
# 简化的工作流程
main           # 主分支（始终可部署）
feature/*      # 功能分支（通过PR合并）

# 工作流程
git checkout main
git checkout -b feature/new-feature
# 开发、提交、推送
git push origin feature/new-feature
# 创建Pull Request，代码审查，合并到main
```

### 5.3 分支命名规范

```bash
# 功能开发
feature/user-authentication
feature/payment-gateway

# 修复bug
fix/login-error
bugfix/memory-leak

# 热修复
hotfix/security-patch
hotfix/critical-bug

# 发布
release/v1.0.0
release/2.1.0

# 实验
experiment/new-ui
experiment/alternative-approach
```

## 6. 远程分支操作

### 6.1 查看远程分支

```bash
# 查看远程分支
git branch -r

# 查看所有远程分支的详细信息
git remote show origin

# 查看远程和本地分支
git branch -a
```

### 6.2 同步远程分支

```bash
# 从远程获取最新信息（不合并）
git fetch origin

# 从远程获取并合并到当前分支
git pull origin feature-login

# 推送本地分支到远程
git push origin feature-login

# 推送所有分支
git push --all origin

# 推送并设置上游分支
git push -u origin feature-login

# 删除远程分支
git push origin --delete feature-login
```

### 6.3 跟踪远程分支

```bash
# 设置本地分支跟踪远程分支
git branch --set-upstream-to=origin/feature-login feature-login

# 查看跟踪关系
git branch -vv

# 创建跟踪远程分支的本地分支
git checkout -b feature-login origin/feature-login
```

## 7. 分支最佳实践

### 7.1 分支管理原则

```bash
# 1. 保持主分支稳定
# main/master分支应该始终是可部署的状态

# 2. 短期分支
# 功能分支应该生命周期短，及时合并和删除

# 3. 频繁提交
# 在功能分支上频繁提交，保持小步快跑

# 4. 定期合并
# 定期将主分支的更改合并到功能分支，减少冲突

# 5. 有意义的分支名称
# 使用清晰、描述性的分支名称
```

### 7.2 工作流建议

```bash
# 开始新功能时
git checkout main
git pull origin main
git checkout -b feature/your-feature-name

# 开发过程中
git add .
git commit -m "描述性的提交信息"
git push origin feature/your-feature-name

# 功能完成后
git checkout main
git pull origin main
git merge feature/your-feature-name
git push origin main
git branch -d feature/your-feature-name
```

## 8. 实用技巧

### 8.1 分支比较

```bash
# 查看分支包含的提交
git log feature-login..main

# 查看独有的提交
git log main..feature-login

# 查看所有分支的提交图
git log --graph --oneline --all

# 查看分支最近一次提交
git log -1 feature-login
```

### 8.2 暂存和恢复

```bash
# 暂存当前工作
git stash

# 在不同分支间切换时暂存工作
git checkout -b temp-branch
git stash pop

# 查看暂存列表
git stash list

# 应用并删除暂存
git stash pop

# 应用但不删除暂存
git stash apply
```

### 8.3 清理分支

```bash
# 查看已合并到当前分支的分支
git branch --merged

# 查看未合并的分支
git branch --no-merged

# 删除所有已合并的本地分支
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# 清理远程已删除的分支引用
git remote prune origin
```

## 9. 常见问题解决

### 9.1 分支丢失恢复

```bash
# 查找丢失的commit
git fsck --lost-found

# 使用reflog恢复分支
git reflog
git checkout -b recovered-branch abc1234
```

### 9.2 错误分支操作

```bash
# 如果在错误的分支上提交
git checkout correct-branch
git cherry-pick wrong-branch
# 然后重置错误分支
git checkout wrong-branch
git reset --hard HEAD~1
```

### 9.3 大文件导致问题

```bash
# 如果分支包含大文件导致问题
git filter-branch --tree-filter 'rm -rf path/to/large/files' HEAD
```

## 10. 总结

### 关键要点

1. **分支是轻量级的**：Git分支创建和切换非常快速，应该频繁使用
2. **选择合适的合并策略**：了解何时使用merge或rebase
3. **遵循工作流程**：使用适合团队的分支管理策略
4. **保持历史清晰**：使用有意义的提交信息和分支名称
5. **定期清理**：及时删除已完成的分支

### 练习建议

1. 创建多个功能分支进行并行开发
2. 练习各种合并场景，包括处理冲突
3. 尝试不同的分支管理策略
4. 熟练掌握远程分支操作

## 参考资源

- [Git官方文档 - 分支](https://git-scm.com/book/zh/v2/Git-分支-分支简介)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
