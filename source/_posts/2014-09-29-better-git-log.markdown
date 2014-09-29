---
layout: post
title: "更好的git log"
date: 2014-09-29 11:05:41 +0800
comments: true
categories: Git使用
---
`git log`对于使用git的"码农们"一定非常熟悉，如：

 git log  命令是查看全部提交日志

 git log -2  查看最近2次的提交日志

 git log -p  查看历史纪录以来哪几行被修改

 git log --pretty=oneline 查看历史提交日志，单行显示


以上是我们经常使用过的命令，接下来将谈谈如何更好的使用git log来解决使用过程中遇到的需求：（大家有木有遇到呢...）

**更清楚的显示单行提交历史**

git log --pretty=online 显示如下：

![git log --pretty=online](http://strivingboy.github.com/images/2014-09-29-oneline.png)

如何图形化显示更清晰的提交历史呢？

git log --graph --decorate --pretty=oneline --abbrev-commit --all

![git log lola](http://strivingboy.github.com/images/2014-09-29-lola.png)

能不能再清楚点呢？比如：显示提交时间、作者.....当然可以啦

git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit

![git log lola](http://strivingboy.github.com/images/2014-09-29-lola.png)


** 参考链接 ** 

- <u>https://www.kernel.org/pub/software/scm/git/docs/git-log.html </u>

- <u>https://coderwall.com/p/euwpig </u>

