---
layout: post
title: "Ctrl+z in linux terminal"
date: 2013-07-27 10:35
comments: true
categories: 
  - linux
  - terminal
---

用惯Windows的人，刚转到Linux下经常会碰到一个**囧**境：

使用终端（`Terminal`）的时候，经常会下意识地按下Windows下的撤销组合键：`Ctrl+z`，结果经常会导致目前的工作突然不见了，然后又会下意识地直接关闭`Terminal`，然后又会经常发现诸如*文件缓存已存在*，*端口已被占用*等等情况。

其实在Linux下，`Ctrl+z`是挂起（`suspend`）一个任务的命令：

```bash
^Z
[1]  + 6084 suspended  noglob rake preview
```

这时我们要做的工作，不是直接`X`掉`Terminal`，而是应该唤回我们被挂起的工作。如果唤回在Linux下被挂起的任务，是本篇要讲的内容。

<!-- more -->

以下是几条常用的命令：

* jobs

`jobs`命令可以查看后台的工作状态：

```bash
$ jobs
[1]  + suspended  noglob rake preview
```

终端显示有一个任务被挂起（suspended）。接下来有两种选择：`bg`和`fg`

* bg

`bg %jobnumber`命令可以将任务放到后台执行

```bash
$ bg
[1]  + 6084 continued  noglob rake preview
```

终端显示有任务被继续了。

这时任务还是在后台继续执行的，我们时不时可能会看到一些输出信息显示在终端上。

* bg

如果要把任务拿到前台来进行处理的话，就需要使用`fg`命令。

当只有一个后台任务的话，可能直接执行`fg`命令，否则就要在其后跟上`jobs`命令里显示的任务号（job number）。


#### P.S.

经常忘记这个命令，记下来提醒自己。**求轻喷。**
