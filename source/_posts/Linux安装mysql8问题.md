---
title: Linux安装mysql8问题
date: 2021-03-24 12:33:16
tags: 日常问题
categories: 
- 日常
- 问题
---

> 昨天还心想11点准时睡觉结果就遇到了问题搞到凌晨2点差点不用睡了😢，有必要记录一下<!--more-->

这次是在一台CentOS安装Mysql数据库，大家可能都直接用rpm或者如果是Debian系统就是apt的方式来去安装mysql。这个确实会比较省心但安装的基本上是老版本5.7这种最后一个开源版本

这里是在官网下载8版本的linux通用包进行安装，其实关于这个的安装步骤我在另一篇文章有写。按照这篇文章的步骤会减少很多问题，

但昨天遇到了新问题

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210324025242.png)

图上是已经解决得时候（主要问题不是图上的端口前面先解决了后面才会有），开启mysql.server时问题提示就是这个

```shell
Starting MySQL
..The server quit without updating PID file (/usr/local/mysql/var/MyServer.pid). ... failed!
```

或

```shell
Starting MySQL.Logging to '/etc/mysql/localhost.localdomain.err'.
.... ERROR! The server quit without updating PID file (/etc/mysql/localhost.localdomain.pid).
```

按照网上解决方案都逃不了下面这七种对我来说是没用的。但像端口占用这种还是比较常见的，然后就是安装步骤有问题漏了权限或者默认路径映射。其中像my.cnf这种配置的解决方案对mysql8是没用的因为mysql8没有这些东西都是内置默认，第二是权限的问题总之按照之前那篇文章安装其实都不会出现这些问题

> 1.可能是/usr/local/mysql/data/rekfan.pid文件没有写的权限
> 解决方法 ：给予权限，执行 “chown -R mysql:mysql /var/data” “chmod -R 755 /usr/local/mysql/data”  然后重新启动mysqld！

> 2.可能进程里已经存在mysql进程
> 解决方法：用命令“ps -ef|grep mysqld”查看是否有mysqld进程，如果有使用“kill -9  进程号”杀死，然后重新启动mysqld！

> 3.可能是第二次在机器上安装mysql，有残余数据影响了服务的启动。
> 解决方法：去mysql的数据目录/data看看，如果存在mysql-bin.index，就赶快把它删除掉吧，它就是罪魁祸首了。

> 4.mysql在启动时没有指定配置文件时会使用/etc/my.cnf配置文件，请打开这个文件查看在[mysqld]节下有没有指定数据目录(datadir)。
> 解决方法：请在[mysqld]下设置这一行：datadir = /usr/local/mysql/data

> 5.skip-federated字段问题
> 解决方法：检查一下/etc/my.cnf文件中有没有没被注释掉的skip-federated字段，如果有就立即注释掉吧。

> 6.错误日志目录不存在
> 解决方法：使用“chown” “chmod”命令赋予mysql所有者及权限

> 7.selinux惹的祸，如果是centos系统，默认会开启selinux
> 解决方法：关闭它，打开/etc/selinux/config，把SELINUX=enforcing改为SELINUX=disabled后存盘退出重启机器试试。

花了两三个小时主要是google都翻了几页然后都尝试了不够也就上面这七种。最后才想起来去看localhost.localdmain.err文件

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210324133642.png)

我这边问题的根源就是temp下的mysql.sock、mysql.sock.lock、mysqlx.sock、mysql.sock.lock，删除完事成功启动服务重新生成。应该是之前服务残留。开始初始化安装mysql初始化密码没记录但又不能像以前版本可通过配置文件查那个随机密码。只能重新初始化这就是问题的根源，之前初始化的已经启动过了生成了这些。

如果不是我上面这个，是端口占用的话就更简单了

* lsof -i:3306
* kill -9 5398

```mysql
[root@localhost hao]# lsof -i:3306
COMMAND  PID  USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
mysqld  5398 mysql   36u  IPv6  77510      0t0  TCP *:mysql (LISTEN)
[root@localhost hao]# kill -9 5398
```

整完睡觉🐱‍👤

