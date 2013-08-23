---
layout: post
title: "Shared Directory on ProFTPD for All Users"
date: 2013-08-23 08:55
comments: true
categories: 
  - ubuntu
  - proftpd
---

## Ubuntu下为ProFTPD中每个单独用户建立公共目录

在`ProFTPD`使用中，如果我们设置每个用户只能操作其对应的目录时，设置公共目录就成为一件比较麻烦的事。

本文采用`ProFTPD`提供的`mod_vroot`解决这个问题。

<!-- more -->

### 安装`mod_vroot`模块：

```bash
$ sudo apt-get install proftpd-mod-vroot
```

### 配置vroot

* 修改`/etc/proftpd/modules.conf`，添加如下语句，引入`mod_vroot`：

```bash
LoadModule mod_vroot.c
```

* 修改`/etc/proftpd/proftpd.conf`, 去掉下面语句的注释：

```bash
Include /etc/proftpd/virtuals.conf
```

*注意这里是使用 Ubuntu 的 apt 安装的 ProFTPD 的话，这句配置项是有错误的，需要修改！*

* 修改`/etc/proftpd/virtuals.conf`：

```bash
<IfModule mod_vroot.c>

  VRootEngine on

  DefaultRoot ~
  VRootAlias /home/ftp/shared ~/shared

</IfModule>
```

这个配置表示，我们将`/home/ftp/shared`设置为共享目录，并映射为每个用户的`~/shared`。

* 重启ProFTPD服务：

```bash
$ service proftpd restart
```

## 参考资料

* [ProFTPD module mod_vroot](http://www.castaglia.org/proftpd/modules/mod_vroot.html#VRootServerRoot)
