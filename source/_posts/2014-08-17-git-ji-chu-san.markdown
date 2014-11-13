---
layout: post
title: "Git 基础(三)"
description: "Git 使用基础"
date: 2014-08-17 19:34:55 +0800
comments: true
categories: Git使用
---


前两篇文章介绍了git的常用命令，基本已经能够满足我们的日常使用，这篇将谈谈使用过程中疑惑的地方，比如：何时使用`git rebase`何时使用`git merge`等

**1、Merging VS Rebasing**

`git merge` 和 `git rebase` 命令都是用来将一个分支合并到另一个分支，只是用不同的方式罢了。下面看下它们的不同：

<!--more-->

**Case:**
	
当你开发一个独立的功能，为此建立了一个特性分支（假设是feature_branch）提交代码，其他团队成员在master分支上提交，结果必然有一个分叉的提交历史,现在假设你发现master分支上新的提交和你开发的功能有关系，为了将这些新的提交合并到feature_branch,于是就有两种选择：merging 或 rebasing

**Merging**

最简单的操作就是将master分支合并到feature_branch，如下：

	git checkout feature_branch
	git merge master
	
或者

	git merge master feature_branch
	
最终会在feature_branch上创建一个新的合并提交记录。

`merging` 是一个友好的命令，因为它是一个没有破坏性的操作，现有的分支不会有任何形式的改变，因此也就避免了`rebasing` 一些潜在的缺陷, 相反，这就意味着每一次`merging`都会在feature_branch会遗留一次额外的提交，当master分支非常活跃时，这将导致feature_branch分支的历史记录初步变大，这时查看工程历史记录，就会发现各种分叉，很难理解。

**Rebasing**

作为`merging`的替代方法，我们也可以将feature_branch分支 rebase到master分支，命令如下：

	git checkout feature_branch
	git rebase master

结果将feature_branch上所有提交移动到了master分支的最前面，很高效，但与 merge 不同的是 rebase会重写工程提交历史，就像是在master上重新提交了一遍一样,而不是想mege那样创建一个新的合并提交记录。

`rebasing`最大的优点便是它可以得到一个很清晰的历史记录，首先，它避免了`merging`产生的那个不必要的提交记录，其次，`rebasing`的结果是一个完美的线型提交历史，可以很清楚的看到工程的变化。当然：`rebasing`也存在潜在的问题：1、安全性（在下面的rebase黄金法则中介绍） 2、可追溯性，即：`rebasing`或失去 `merging`创建的合并记录，从而无法查看合并记录点。

**Rebasing黄金法则**

理解了`rebasing`的使用，下来就是何时使用:`The golden rule is to never use it on public branches.`

举个例子：
假设你讲 master 分支rebase 到 feature_brauch分支,结果将master上所有提交移动到feature_brauch的最前面，问题是：这次合并操作只影响feature_brauch分支，所有其他开发这依然在original master分支上工作，由于`rebasing`是重写提交历史，git 会认为所有历史提交已经与其他开发者提交的不同，同步这两个分支唯一的办法是将feature_brauch分支又`merging`到master分支，结果是两次提交集合中大部分包含了相同的修改，不用说,这是一个非常混乱的局面。因此，当执行`rebasing`操作时，时常问下自己：`是不是有其他人在关注改分支？`如果是，则考虑其它方式，否则则可以安全使用`rebasing`。

**2、Reset, Checkout, and Revert**

`git reset`、`git checkout`和`git revert`都是用来撤销对代码仓库的各种修改，前两个命令可以操作提交或单个文件，他们如此相似，在某种场合下容易混谣，下面我们将谈谈它们的区别。

我们都知道git 有三个主要的组件：1、工作区（Working Directory）2、暂存区（Staged Snapshot）3、提交历史(Commit History),理解这三个组件就不难掌握`git reset`、`git checkout`和`git revert`的区别，

`Reset`:命令可以撤销当前分支的某些提交，如：

	git checkout feature_branch
	git reset HEAD^2
	
上面的命令将当前分支回退了两次提交，`Reset`命令对于想撤销某些提交（前提是当前分支只有自己提交）时非常方便，`Reset`命令有一下标记：

+ --soft:使用该标记不会影响暂存区和工作区
+ --mixed:使用该标记后，暂存区会更新到了指定的提交，工作区不影响，该标记是默认的
+ --hard:暂存区和工作区均更新到指定提交

*git reset --mixed HEAD* 会将本地暂存的修改从暂存区取出来，如果想彻底删除未提交的修改，可以使用*git reset --hard HEAD* （危险：本地修改将不会恢复了哦）这是`Reset`常用的两种方式。

`Checkout`:应该非常熟悉了，首先就是切换分支：

	git checkout master

git 内部会将HEAD游标移动到master分支，并且更新工作区，因为它会重写本地修改，所以git强制我们在`Checkout`之前去提交或暂存工作区中的修改。
	
	git checkout .
	
注意该命令危险！它和 *git reset --hard HEAD* 类似，会彻底删除未提交的修改,不可恢复。

`Revert`:命令是用一个新的提交来撤销某次历史提交，不会重写提交历史，是安全可恢复的。因此可用于公开提交的分支，这点与`Reset`不同，`Reset`仅用于私有特性分支。
