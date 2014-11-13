---
layout: post
title: "Git 使用基础(二)"
description: "Git 使用基础"
date: 2014-08-09 23:09:56 +0800
comments: true
categories: Git使用
---

**git checkout**

`git checkout` 命令提供三种功能：1、查看文件历史修改 2、切换到某次提交（或某次提交的特定文件） 3、切换分支

用法：
    
    git checkout master

从当前分支切换到master分支

    git checkout <commit> <file>

将本地文件file 更新到 commit 次提交

<!--more-->

例子：

    git log --oneline 查看下历史提交如下：

    ```
    ad90c97 modify link
    9de0669 add oneline.png
    b83c889 modify test.txt
    ```

    git checkout ad90c97 查看修改内容

    git checkout b83c889 test.txt 查看对test.txt 文件的修改

**git revert**

`git revert` 命令用来撤销历史中的某次提交，并且不会撤销其后面的提交历史 

用法：
    
    git revert <commit>

撤销 commit 次提交

**git reset**

`git reset` 命令用来撤销历史中的某次提交，也会撤销其后面的提交历史以

用法：
    
    git revert <commit>

撤销 commit 次提交
    
    git revert --hard <commit>

将当前分之会退到 commit 提交，并且会撤销本地未提交的修改（不安全）

**git clean**

`git clean` 命令用来移除本地未暂存的文件，相当于`git reset --hard`

用法：
    
    git clean -df

移除本地未暂存的修改以及文件
  
**git rebase**

`git rebase` 命令用来将一个分支移动到某个分支（如B分支），在B分支上作为最新的提交（结果相当于 git merge）

用法：
    
    git rebase <base>

将当前分支移动到base分支

**git merge**

`git merge` 命令用来将一个分支合并到某个分支,合并的结果作为一次新的提交

用法：
    
    git merge <branch>

合并branch分支合并到当前分支，和`git rebase`的区别以及各自优缺点见这里: **[git rebase vs git merge](http://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)**

**git fetch**

`git fetch` 命令用来获取远端仓库最新代码到本地,不会自动合并（相当于开了一个tmp分支）

用法：
    
    git fetch <remote> 或 git fetch <remote> <branch>

**git pull**

`git pull` 命令用来获取远端仓库最新代码并且和本地代码自动合并

用法：
    
    git pull <remote> 

相当于：git fetch <remote> ＋ git merge origin/<current-branch>.

    git pull --rebase <remote>

相当于：git fetch <remote> + git rebase origin/<current-branch>.

实际上，很多开发者都使用 `git pull --rebase` git 也提供了对应的配置如下：

    git config --global branch.autosetuprebase always

详细理由见这里： **[git pull 和 git pull --rebase的不同](http://stackoverflow.com/questions/18930527/difference-between-git-pull-and-git-pull-rebase)**

**git push**

`git push` 命令用来将本地提交推送到远程仓库

用法：
    
    git push <remote> <branch>

`git push <remote> --tags` 将本地标签推送到远端，默认标签不会自动推送


**git remote**

`git remote` 命令用来列出每个远程库的简短名字

用法：
    
    git remote

要查看当前配置有哪些远程仓库，可以用`git remote`命令，它会列出每个远程库的简短名字。在克隆完某个项目后，至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库

    git remote -v 

结果如下：

    origin  https://github.com/strivingboy/strivingboy.github.com.git (fetch)
    origin  https://github.com/strivingboy/strivingboy.github.com.git (push)

显示对应的克隆地址

    git remote add <shortname> <url>

添加一个新的远程仓库，可以指定一个简单的名字 shortname，以便将来引用

    git remote rm <shortname>

删除远端名为shortname的仓库

    git remote rename <old-name> <new-name>

将远程仓库 old-name 重命名为 new-name

下一篇：[Git 使用基础(三)](http://strivingboy.github.com/blog/2014/08/17/git-ji-chu-san/)


