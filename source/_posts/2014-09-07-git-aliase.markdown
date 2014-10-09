---
layout: post
title: "让Git命令更简单（Git alias）"
date: 2014-09-07 11:10:44 +0800
comments: true
categories: Git使用
---

简单使用 git 命令的方式莫过于添加别名了，alias时linux下一个常用命令详见：<u>http://en.wikipedia.org/wiki/Alias_(command) </u> 

**如何添加别名**

假设我们想用 `git ci`代替`git commit`, 我们可以添加如下命令到 `~/.gitconfig` 文件中

	［alias］
		ci = commit

如果不习惯手动编辑 config 文件，也可以使用命令 `git config alias.ci commit`来代替，如果想在自己机器的任何地方都使用改别名，则可添加 `--global` 标记
	
	git config --global alias.ci commit

<!--more-->

**常用别名**

```
[alias]
  st = status
  ci = commit
  br = branch
  co = checkout
  df = diff
  ad=add
  cp = cherry-pick
  lg = log -p
 ```

**其他别名**

1.初始化git仓库

`this = !git init && git add . && git commit -m \"initial commit\"`

2.暂存

`sl = stash list`

`sa = stash apply`

`ss = stash save`


2.删除已经删除的文件

`rd  = !git ls-files -z --deleted | xargs -0 git rm`

3.清空未暂存的文件

`cd = git clean -df`

4.撤销本地所有修改

`cl = git checkout .`

5.列出所有别名

`alias = config --get-regexp 'alias.*'`


以上是我的常用git 别名，其他有意思的请在下面评论哈.....


** 参考链接 ** 

- <u>http://en.wikipedia.org/wiki/Alias_(command) </u>
- <u>http://git-scm.com/docs/ </u>

