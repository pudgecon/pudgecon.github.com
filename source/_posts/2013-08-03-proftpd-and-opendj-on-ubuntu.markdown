---
layout: post
title: "Proftpd and OpenDJ on Ubuntu 12.04"
date: 2013-08-03 12:23
comments: true
categories: 
  - proftpd
  - opendj
  - ubuntu
---

## 在Ubuntu 12.04上实现proftpd基于OpenDJ的LDAP身份认证

### 系统环境

  * 操作系统：Ubuntu 12.04
  * OpenDJ:
    [opendj_2.6.0-1_all.deb](https://download.forgerock.com/downloads/enterprise/opendj/2.6.0/opendj_2.6.0-1_all.deb)
  * Proftpd: 1.3.4a

### 安装OpenDJ

1. 安装`OpenDJ`前，首先安装JAVA环境。

```bash
$ sudo apt-get install default-jre
```

可以使用

```bash
$ java -version
java version "1.6.0_27"
OpenJDK Runtime Environment (IcedTea6 1.12.6)
(6b27-1.12.6-1ubuntu0.12.04.2)
OpenJDK 64-Bit Server VM (build 20.0-b12, mixed mode)
```

查看安装的`JAVA`版本。

2. 下载`OpenDJ`的`deb`安装包。安装命令：

```bash
$ sudo dpkg -i /path/to/opendj_2.6.0-1_all.deb
Selecting previously unselected package opendj.
(Reading database ... 185569 files and directories currently installed.)
Unpacking opendj (from opendj_2.6.0-1_all.deb) ...
 
Setting up opendj (2.6.0) ...
```

`.deb`安装包将`OpenDJ`安装在`/opt/opendj`.

安装的文件默认是属于`root`的（owned by root by default），以便于`OpenDJ`更方便地监听`389`和`636`端口。

3. 配置`OpenDJ`：

```bash
$ /path/to/opendj/setup --cli
READ THIS SOFTWARE LICENSE AGREEMENT CAREFULLY. BY DOWNLOADING OR
INSTALLING
THE FORGEROCK SOFTWARE, YOU, ON BEHALF OF YOURSELF AND YOUR COMPANY,
AGREE TO
BE BOUND BY THIS SOFTWARE LICENSE AGREEMENT. IF YOU DO NOT AGREE TO
THESE
TERMS, DO NOT DOWNLOAD OR INSTALL THE FORGEROCK SOFTWARE.
 
...
 
Please read the License Agreement above.
You must accept the terms of the agreement before continuing with the
installation.
Accept the license (Yes/No) [No]:Yes
 
What would you like to use as the initial root user DN for the Directory
Server? [cn=Directory Manager]:
Please provide the password to use for the initial root user:
Please re-enter the password for confirmation:
 
Provide the fully-qualified directory server host name that will be used when
generating self-signed certificates for LDAP SSL/StartTLS, the administration
connector, and replication [opendj.example.com]:
 
On which port would you like the Directory Server to accept connections from
LDAP clients? [389]: 
 
On which port would you like the Administration Connector to accept
connections? [4444]: 
 
Do you want to create base DNs in the server? (yes / no) [yes]: 
 
Provide the base DN for the directory data: dc=example,dc=com
Options for populating the database:
 
    1)  Only create the base entry
    2)  Leave the database empty
    3)  Import data from an LDIF file
    4)  Load automatically-generated sample data
 
Enter choice [1]: 1
 
Do you want to enable SSL? (yes / no) [no]:
 
Do you want to enable Start TLS? (yes / no) [no]:
 
Do you want to start the server when the configuration is completed? (yes / no) [yes]:
 
Setup Summary
=============
LDAP Listener Port:            389
Administration Connector Port: 4444
LDAP Secure Access:            disabled
Root User DN:                  cn=Directory Manager
Directory Data:                Create New Base DN dc=example,dc=com.
Base DN Data: Only Create Base Entry (dc=example,dc=com)
 
Start Server when the configuration is completed
 
What would you like to do?
 
    1)  Set up the server with the parameters above
    2)  Provide the setup parameters again
    3)  Print equivalent non-interactive command-line
    4)  Cancel and exit
 
Enter choice [1]:
 
See /var/.../opendj-setup...log for a detailed log of this operation.
 
Configuring Directory Server ..... Done.
Importing LDIF file /path/to/Example.ldif ........... Done.
Starting Directory Server ........... Done.
 
To see basic server configuration status and configuration you can
launch \
/path/to/opendj/bin/status
```

基本上可以采用默认配置，除了密码以及`base DN`。

4. 检查`OpenDJ`状态：

```bash
$ sudo /opt/opendj/bin/status
 
 
>>>> Specify OpenDJ LDAP connection parameters
 
Administrator user bind DN [cn=Directory Manager]:
 
Password for user 'cn=Directory Manager':
 
          --- Server Status ---
Server Run Status:        Started
Open Connections:         1
 
          --- Server Details ---
Host Name:                ubuntu.example.com
Administrative Users:     cn=Directory Manager
Installation Path:        /opt/opendj
Version:                  OpenDJ 2.6.0
Java Version:             version
Administration Connector: Port 4444 (LDAPS)
 
          --- Connection Handlers ---
Address:Port : Protocol               : State
-------------:------------------------:---------
--           : LDIF                   : Disabled
0.0.0.0:161  : SNMP                   : Disabled
0.0.0.0:389  : LDAP (allows StartTLS) : Enabled
0.0.0.0:636  : LDAPS                  : Enabled
0.0.0.0:1689 : JMX                    : Disabled
0.0.0.0:8080 : HTTP                   : Disabled
 
          --- Data Sources ---
Base DN:     dc=example,dc=com
Backend ID:  userRoot
Entries:     2002
Replication:
```

如出现上述，则表明安装成功。

### 安装Proftpd

1. Proftpd可以直接使用apt-get安装：

```bash
$ sudo apt-get install proftpd-mod-ldap
```

采用`standalone`方式安装

2. 修改配置文件 `/etc/proftpd/modules.conf`

将`25行`左右的注释打开，结果如下：

```
LoadModule mod_ldap.c
```

3. 修改配置文件`/etc/proftpd/proftpd.conf`

将`132行`左右的注释打开，结果如下：

```
Include /etc/proftpd/ldap.conf
```

4. 最后是修改配置文件`/etc/proftpd/ldap.conf`，这步是最为关键的。

首先，我们使用OpenDJ提供的[`Example.ldif`](http://opendj.forgerock.org/Example.ldif)生成一些测试数据：

```bash
$ sudo /opt/opendj/bin/stop-ds # 首先停止OpenDJ

$ sudo /opt/opendj/bin/import-ldif \
 -b dc=example,dc=com \
 -n userRoot \
 -l /path/to/Example.ldif
```

可以使用以下命令检测是否导入成功：

```bash
$ sudo /opt/opendj/bin/ldapsearch \
  -b dc=example,dc=com \
  -D "cn=Directory Manager" \
  -w 123456 \
  "(cn=Sam Carter)"

dn: uid=scarter,ou=People,dc=example,dc=com
uid: scarter
userPassword: {SSHA}FwNrH209JqP/KjHKRTzEUFMKQYuaHeVf5u9GlQ==
facsimileTelephoneNumber: +1 408 555 9751
departmentNumber: 1000
preferredLanguage: en-gb
givenName: Sam
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: top
cn: Sam Carter
sn: Carter
telephoneNumber: +1 408 555 4798
street: 60 Queen Square
homeDirectory: /home/scarter
roomNumber: 4612
l: Bristol
mail: scarter@example.com
uidNumber: 1002
ou: Accounting
ou: People
gidNumber: 1000
```

重新启动`OpenDJ`：

```bash
$ sudo /opt/opendj/bin/start-ds
```

修改配置文件`/etc/proftpd/ldap.conf`

```
<IfModule mod_ldap.c>
LDAPServer ldap://localhost/??sub
LDAPBindDN "cn=scarter,dc=example,dc=com" "sprain"
LDAPUsers dc=People,dc=example,dc=com (uid=%u) (uidNumber=%u)
</IfModule>
```

这里的`LDAPServer`为配置`OpenDJ`时填的`Hostname`。

注意这后面的`/??sub`，它指定了LDAP的`SearchScopr`为`wholeSubtree`，否则ftp连接时，查询的请求scope为`baseObject`，就查不到想要的结果了。

`LDAPBindDN`为我们在Example.ldif里面挑的`Accounting Managers`之一。
