---
layout: post
title: "Install OpenLDAP and Proftpd on Ubuntu 12.04"
date: 2013-08-23 08:06
comments: true
categories: 
  - ubuntu
  - ldap
  - openldap
  - proftpd
---

## Ubuntu 12.04上实现Proftpd基于OpenLDAP的LDAP身份认证

### 系统环境

  * 操作系统：Ubuntu Server 12.04

<!-- more -->

### 安装OpenLDAP

`Ubuntu`上安装`OpenLDAP`比较简单：

```bash
$ sudo apt-get install slapd ldap-utils
```

具体使用文档可以参考[OpenLDAP的Ubuntu官方文档](https://help.ubuntu.com/lts/serverguide/openldap-server.html)。

### 配置组织结构

* 添加新的`basedn`，这里以`dc=example,dc=com`为例：

{% include_code lang:bash openldap_and_proftpd/backend.ldif %}

* 添加新的`groups`和`users`：

{% include_code lang:bash openldap_and_proftpd/organization.ldif %}

这里添加了一个管理员`admin`和一个用户`bill`。

* 使用下面命令导入：

```bash
$ sudo ldapadd -Y EXTERNAL -H ldapi:/// -f backend.ldif 
$ sudo ldapadd -x -D cn=admin,dc=example,dc=com -W -f organization.ldif
```

### 安装Proftpd

```bash
$ sudo apt-get install proftpd-mod-ldap
```

这个`deb`包包含了编译的ldap模块（mod_ldap）。

### 配置Proftpd

* 修改`/etc/proftpd/proftpd.conf`：

去掉下面几行注释：

```
DefaultRoot       ~
RequireValidShell off

Include /etc/proftpd/ldap.conf
```

* 修改`/etc/proftpd/modules.conf`：

去掉下面一行注释，加载ldap模块：

```
LoadModule mod_ldap.c
```

* 修改`/etc/proftpd/ldap.conf`：

这个配置文件最为关键：

{% include_code lang:bash openldap_and_proftpd/ldap.conf %}

其中的`LDAPDefaultUID`与`LDAPDefaultGID`为proftpd用户的`uid`与`gid`，可以通过以下命令查看：

```bash
$ cat /etc/passwd | grep proftpd
```

* 创建FTP目录

以如上配置为例，我们需要在`/home`目录下建立`FTP`目录，并赋予相应权限：

```bash
$ sudo mkdir /home/ftp
$ sudo chown proftpd:nogroup /home/ftp
```


### 参考资料：

[如何在Ubuntu Desktop 12.04下‧安裝OpenLDAP及SAMBA服務](http://www.nep-hk.com/drupal/?q=node/150)


** 有问题请留言，如有错误，欢迎指正。**
