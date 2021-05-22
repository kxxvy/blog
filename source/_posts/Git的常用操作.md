---
title: Git的常用操作
date: 2020-11-10
tags: [git]
---

以下是在平时工作中经常会用到的git命令，现全部列出来

<!-- more -->

1.创建分支

```
git branch develop
```

2.查看本地分支：

```
git branch
```

3.查看远程分支：

加上`-a`参数可以查看远程分支，远程分支会用红色表示出来（如果你开了颜色支持的话）

```
git branch -a
```

4.切换分支

```
git checkout branch_name
```

5.删除分支
删除本地分支

```
git branch -d branch_name
```

删除远程分支

```
git push origin -d branch-name
```

6.修改重命名

1）本地分支重命名(还没有推送到远程)

```
git branch -m oldName newName
```

2）远程分支重命名 (已经推送远程-假设本地分支和远程对应分支名称相同)

a. 重命名远程分支对应的本地分支

```
git branch -m oldName newName
```

b. 删除远程分支

```
git push --d origin oldName
```

c. 上传新命名的本地分支

```
git push origin newName
```

d.把修改后的本地分支与远程分支关联

```
git branch --set-upstream-to origin/newName
```

7.如果远程新建了一个分支，本地没有该分支。

可以利用 `git checkout --track origin/branch_name` ，这时本地会新建一个分支名叫 `branch_name` ，会自动跟踪远程的同名分支 `branch_name`。

8.如果本地新建了一个分支 `branch_name`，但是在远程没有。

这时候 `push` 和 `pull` 指令就无法确定该跟踪谁，一般来说我们都会使其跟踪远程同名分支，所以可以利用 `git push --set-upstream origin branch_name` ，这样就可以自动在远程创建一个 `branch_name` 分支，然后本地分支会 `track` 该分支。后面再对该分支使用 `push` 和 `pull` 就自动同步。

```
git push --set-upstream origin branch_name
```

9.合并分支到`master`上

首先切换到`master`分支上

```
git checkout master
```

如果是多人开发的话 需要把远程`master`上的代码`pull`下来

```
git pull origin master
```

然后我们把`dev`分支的代码合并到`master`上

```
git merge dev
```

然后查看状态

```
git status
```
