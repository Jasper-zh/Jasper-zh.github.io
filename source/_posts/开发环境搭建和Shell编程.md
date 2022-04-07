---
title: 开发环境搭建和Shell编程
date: 2020-01-12 13:19:43
tags: 总结文档
copyright: false
---



## 1. 开发环境搭建（掌握）
### 1.1 Xshell和Xftp工具
**（1）下载和安装方式**
* 下载地址：https://www.netsarang.com/zh/
* 安装方式：直接一路点击下一步即可，安装过程选择免费版。

**（2）使用方式**
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210412132552.png)



**（3）网络模式设置**

{% note info no-icon %}

这里连接的服务器主机是用VM启的虚拟机，所以才需要配置下它的主机地址并且调整为静态IP。否则的话就不需要（3）（4）（5）步啦！

{% endnote %}



* 设置网络连接模式为NAT模式，步骤如下：

{% note info no-icon %}

1. 打开虚拟机设置
2. 选择网络适配器，选中NAT模式

{% endnote %}

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425195244.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425195328.png)



**（4）修改配置文件**

* 查看虚拟机网络信息

{% note info no-icon %}

1. 打开编辑中虚拟机网络编辑
2. 上一步网络模式已调成NAT，因此选中NAT
3. 打开NAT Settings 可看到子网IP、子网掩码、网关IP
4. 打开DHCP Settings 看IP范围

{% endnote %}

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425195829.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425201458.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425202315.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210425202126.png)

* 使用root用户打开/etc/sysconfig/network-scripts/ifcfg-eno16777736文件，添加内容如下（根据自己的地址填）：

```
BOOTPROTO=static       # 改成静态IP
ONBOOT=yes             # 重启更新
IPADDR=192.168.40.129  # 我的在192.168.40.128与192.168.40.254之间 
GATEWAY=192.168.40.2   # 网关IP按照NAT Settings看到的填写
NETMASK=255.255.255.0  # 子网掩码按照NAT Settings看到的填写
DNS1=114.114.114.114
```

**（5）配置文件生效**
```
使用命令使得配置文件生效：service network restart
```

### 1.2 JDK的下载和安装
**（1）下载和安装方式**

* 下载地址：https://www.oracle.com/java/technologies/javase-downloads.html
* 安装方式：将下载好的jdk安装包通过Xftp工具传输到CentOS系统中，使用tar命令解压即可。

**（2）配置环境变量**
* 使用root用户打开配置文件/etc/profile，向文件末尾追加内容如下：

```
export JAVA_HOME=/usr/javajdk 
export PATH=$JAVA_HOME/bin:$PATH
```

* 保存退出后让文件生效并验证是否配置成功

```shell
source /etc/profile 
javac -version
```

### 1.3 Tomcat的下载和安装
**（1）下载和安装方式**
* 下载地址：https://tomcat.apache.org/download-80.cgi
* 安装方式：将下载好的Tomcat安装包通过Xftp工具传输到CentOS系统中，使用tar命令解压即可。

**（2）启动和关闭方式**
```shell
startup.sh 
shutdown.sh
```

**（3）开放防火墙端口**
```
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT 开启8080端口（暂时开通）
```

**（4）配置环境变量**
* 使用root用户打开配置文件/etc/profile，向文件末尾追加内容。

```
export CATALINA_HOME=/usr/tomcat export PATH=$CATALINA_HOME/bin:$PATH
```
* 保存退出后让文件生效并验证是否配置成功

```
source /etc/profile 
startup.sh
```

**（5）发布Web项目**
* 将Web项目打成war包，通过Xftp工具将war包放在tomcat/webapp目录并启动

### 1.4 Mysql的安装和连接

**（1）安装**
{% btn 'https://yournotes.cn/2021/04/12/Linux%E5%AE%89%E8%A3%85%E6%9C%80%E6%96%B0Mysql/',点击查看,far fa-hand-point-right,larger %}

**（2）远程连接**
安装配置完成后使用管理工具去连接我们的远端Mysql可能会遇到如下问题：
![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210412135526.png)

**（3）解决方案**
* 使用root权限登录数据库后选择mysql库

```mysql
mysql -u root -p 
use mysql;
```
* 查看mysql库中的user表的host值后修改为通配符%或者就自己的地址

```mysql
select host from user where user='root'; 
update user set host='%' where user='root'; 
flush privileges;
```
* 查看修改结果并重新测试

```mysql
select User,Host from user;
```

## 2. Shell编程（熟悉）
### 2.1 基本概念
* Shell是一个命令行解释器，可以接收应用程序或用户命令，然后访问操作系统内核。
* Shell是一个功能相当强大的编程语言，易编写、易调试、灵活性强；

### 2.2 编写第一个程序
* 使用vi工具创建xxx.sh的文件。
* 以`#!/bin/bash`开头并编写代码后保存。

### 2.3 执行Shell程序的方式
* 方式一：./文件名，此方式需要执行权限。
* 方式二：/bin/bash 文件名，此方式不需要执行权限。

### 2.4 变量的定义
**（1）语法格式**
* 定义变量：变量=值
* 撤销变量：unset 变量

**（2）定义规则**

* 变量名称可以由字母、数字和下划线组成，但是不能以数字开头，环境变量名建议大写。
* 不能使用bash里的关键字。
* 中间不能有空格，可以有下划线。
* 在bash中，变量默认类型都是字符串类型，无法直接进行数值运算。
* 变量的值如果有空格，需要使用双引号或单引号括起来。

### 2.5 常用运算符
**（1）算术运算符**

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210412141223.png)

**（2）关系运算符**

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210412141235.png)

### 2.6 流程控制语句
**（1）if判断**
```shell
if [ 条件判断式 ] 
    then
	    程序 
fi
```
**（2）case语句**

```shell
case $变量名 in 
	"值1"） 
		如果变量的值等于值1，则执行程序1 
		;; 
	"值2"） 如果变量的值等于值2，则执行程序2 
		;;
    …省略其他分支… 
    *）如果变量的值都不是以上的值，则执行此程序 
    	;; 
esac
```

**（3）for循环**

```shell
for (( 初始值;循环控制条件;变量变化 )) 
do
	程序 
done
```

**（4）while循环**

```shell
while [ 条件判断式 ] 
do
    程序 
done
```

### 2.7 函数

```shell
[ function ] funname[()] 
{ 
	Action; 
	[return int;] 
}
funname
```

