---
layout: post
title: "更好的git log"
date: 2014-09-29 11:05:41 +0800
comments: true
categories: Git使用
---
`git log`对于使用git的"码农"们一定非常熟悉，如：

 git log  命令是查看全部提交日志

 git log -2  查看最近2次的提交日志

 git log -p  查看历史纪录以来哪几行被修改

 git log --pretty=oneline 查看历史提交日志，单行显示


以上是我们经常使用过的命令，接下来将谈谈如何更好的使用git log来解决使用过程中遇到的需求：（相信大家也回遇到...）

**1.更清楚的显示单行提交历史**

git log --pretty=online 回显示如下：

![git log --pretty=online](http://strivingboy.github.com/_posts/image/online.png)


** 参考链接 ** 

- <u>https://www.kernel.org/pub/software/scm/git/docs/git-log.html </u>

- <u>https://coderwall.com/p/euwpig </u>

