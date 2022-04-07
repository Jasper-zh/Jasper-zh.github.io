---
title: Linux安装最新Mysql
date: 2020-09-12 14:55:39
tags: 总结文档
---

## 前言 

> 之前自个需要在deepin下安装mysql，网上大家都是用软件源安装
>
> `sudo apt-get install -y mysql-server mysql-client`
>
> 是安装的是mariaDB，关于MySQL与mariaDB，mariaDB是MySQL5.7源代码的一个分支，由于Oracle公司买下了Sun，在意识到Oracle可能会将MySQL闭源，于是先分离出了MySQL的衍生版MariaDB。虽然大部分兼容MySQL但是还是在我的项目出现问题，因此还是去装之前项目使用的MySQL8。



## 下载

进入官网下载

{% btn 'https://dev.mysql.com/downloads/mysql/',点击跳转,far fa-hand-point-right,center larger %}

这里我选择的通用包不同版本的都可以用下面方式安装，也可以对应自己linux系统进行选择。当然这里在deepin下安装也可以选择debian9的版本直接安装deb包

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/82cb95b4e70442223fb62f3c0d0ac9a0.png)

或者直接下debian的，如果是红帽系列就是rpm

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/c6b6c862f7c10a7e9030ad9c175b0680.png)

下面只介绍使用通用压缩包的安装方式



## 安装mysql

* 解压并移动到/usr/local

```
sudo tar -xvJf mysql-8.0.19-linux-glibc2.12-x86_64.tar.xz -C /usr/local
```

* 创建软链接

```
cd /usr/local
sudo ln -s mysql-8.0.19-linux-glibc2.12-x86_64 mysql
```

* 添加mysql用户组和mysql用户

```
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
```

* 创建data文件夹存放数据库文件

```
cd /usr/local/mysql
sudo mkdir /usr/local/mysql/data
```

* 修改当前目录拥有者为新建的mysql用户

```
sudo chown -R mysql:mysql ./
```

* 初始化安装

```
sudo ./bin/mysqld --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --initialize
```

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/9ca73aa525dbaa37123e3fb91629aac7.png)

{% note warning simple %}
千万在这里记下初始化密码，8.2几版本没有查看初始密码的地方（虽然有配置可以越过密码）
{% endnote %}

* 开启服务

```
cd /usr/local/mysql-8.0.19-linux-glibc2.12-x86_64/support-files 
sudo ./mysql.server start
```

使用初始密码已经可以登陆了

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/b8ad30123bee4eb4bf545b30fcd73df9.png)

* 设置密码

```mysql
alter user 'root'@'localhost' identified by '123456';
```



## 总结

总之呢就是下载解压安装设密码（中途有些权限还有软连接路径设定是必要的），对于deepin系统来说的话直接使用debian9的deb包进行安装即可更加简单