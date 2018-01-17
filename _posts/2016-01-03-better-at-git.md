---
title: "Better at Git"
excerpt: "Git 与 GitHub 的使用记录与总结。"
modified: 2016-01-03T16:03:49-04:00
categories: 
  - Tools
tags:
  - Git
---

{% include toc title="内容列表" icon="file-text" %}

工作流程图

![Git Image]({{ site.url }}{{ site.baseurl }}/assets/images/git-work-flow.png)
* Workspace：工作区
* Index / Stage：暂存区
* Repository：仓库区（或本地仓库）
* Remote：远程仓库

`
相应的术语
HEAD 这是当前分支版本顶端的别名，也就是在当前分支最近的一个提交
Index 也被称为 Staging Area，是指一整套即将被下一个提交的文件集合。他也是将成为HEAD的父亲的那个commit
Working Copy 代表正在工作的那个文件集
`

## 新建仓库并推送到GitHub
```
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:username/reponame.git
#此命令将项目所在uri命名为origin
git push -u origin master
```

## 设置
```
# 显示设置
git config --list
# 系统设置
git config --system <参数>
# 当前用户设置
git config --global <参数>
# 编辑设置
git config --e --global
```

## 查看历史记录
```
# 单行显示提交记录
git log --pretty=oneline
# 查看命令历史，以便确定要回到未来的哪个版本
# 查看近两次提交改动
git log -p -2
# 查看某次commit的修改内容
git show <commit-hash-id>
# 查看文件每一行的修改者
git blame file
# 查看某个文件的修改历史
git log -p <filename>
# 查看操作记录，可用于 reset 后的重做
git reflog
#回退到某个版本
git reset --hard 3628164
```

## 改动比对
```
# 已修改未暂存阶段
git diff
# 已暂存未提交
git diff --cached
# 已提交未推送
git diff master origin/master
```

## 撤销修改
git reset 有三种恢复模式：--soft 、--mixed 以及--hard 。
* --soft # 还原 HEAD
* --mixed # 默认模式，还原 HEAD、Index 
* --hard #  还原 HEAD、Index、Working Directory
```
# 已修改未暂存
git checkout . # 或
git reset --hard  # == git reset --hard HEAD
# 已暂存未修改
git reset # == git reset --mixed
git checkout .
# 已提交未推送
git reset --hard origin/master
```

## 分支操作
切换分支时，当手头工作没有完成时，先把工作现场 git stash 一下，然后去修复 bug，修复后，再 git stash pop，回到工作现场。
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
```
# 查看本地分支
git branch
# 查看远程分支
git branch -r 
# 查看所有分支
git branch -a
# 创建分支，不切换
git branch name
# 切换分支
git checkout name
# 创建+切换分支
git checkout -b name
# 合并某分支到当前分支
git merge  --no-ff someBranch
# 删除分支
git branch -D name
# 删除远程分支，推送空分支（空格）到远程分支
git push origin :name
```

## 获取其它分支上的提交
```
# 增加参数 -n 不自动提交，只暂存
git cherry-pick -n commit-id
```

## 文件删除和添加
```
# 删除某文件并 staged
git rm
# 恢复方法
git add -i
# 选择revert
# 选择文件区间 1-200等
```

## 合并远程仓库冲突
```
# 以 master 为基础新建一个分支
git checkout -b others-master master
# 拉取远程的更新到 others-master
git pull others-url others-master
# git status 查看冲突 vscode 修改合并
# 提交合并
git commit -am 'merge message'
# 合并冲突后的分支更新合并到 master
git checkout master
git merge --no-ff others-master
# 推送远程仓库
git push origin master
```

## 储藏更新
```
# 当前修改压入 stash 栈
git stash
# 查看 stash 栈历史
git stash list
# 应用某次 stash 记录
git stash apply stash@{id}
# 弹出并应用 stash 栈顶储藏记录
git stash pop
# 删除某个 stash 记录
git stash drop stash@{id}
```

## 修改提交信息
```
# 修改最后一次提交信息，
# 文件有变化则将提交覆盖到最后一次提交，而不会增加新的提交记录的
git commit --amend
# 将多个提交记录合并为一个
git rebase -i HEAD~
```
git rebase 以某次提交为基线，基线以后都视为变动，命令会打开一个临时文件，列出记录列表，pick 表示保留提交记录，squash 表示本次变化合入提交信息不保留，文件写入后会弹出第二个临时文件，用于将合并的记录标识为一个提交信息。

## git remote
```
# 列出远程主机
git remote -v
# 显示远程主机相关信息
git remote show origin
# 添加远程主机
git remote add <主机名> <主机网址>
# 删除远程主机
git remote rm <主机名>
# 远程主机重命名
git remote rename <原名称> <新名称>
```

## git pull
```
# 来取远程分支的更新到本地
git pull <远程主机名> <远程分支名>:<本地分支名>
# 允许同步删除远程主机删除的分支
git pull -p
```

## git push
```
git push <远程主机名> <本地分支名>:<远程分支名>
# 当前分支指定默认主机和分支，允许 git push 默认推送
git push -u origin master
# 覆盖更新
git push --force origin
# 推送标签
git push origin --tags
```

## 远程操作示例
```
# 显示远程分支
git brach -r 
# 取回远程分支更新到本地
git fetch <主机名> <分支名>
# 建立追踪关系
git branch --set-upstream-to=origin/master dev
# 合并更新到某分支
git merge origin/master
git rebase origin/master -i
# 以远程分支为基础创建一个新的分支
git checkout -b newBranch origin/master
```

## 删除分支的历史记录
```
# 1. Checkout 一个没有历史记录的分支
git checkout --orphan latest_branch
# 2. 将所有文件暂存
git add -A
# 3. 本地提交
git commit -m "commit message"
# 4. 删除原分支
git branch -D master
# 5.重新命名为原分支
git branch -m master
# 6.远程仓库强制更新
git push -f origin master
```

## 子模块

Git 允许克隆另外一个仓库到的项目中作为子项目，并且保持提交的相对独立。
```
// 将子项目加入当前仓库，各自提交记录独立
git submodule add git://github.com/chneukirchen/rack.git rack
```
如果 git clone 了一个带有子仓库的项目，将得到一个包含子仓库的目录，其中没有内容，必须运行两个命令，初始化并更新拉取
```
git submodule init
git submodule update
// 或
git submodule update --init --recursive
```