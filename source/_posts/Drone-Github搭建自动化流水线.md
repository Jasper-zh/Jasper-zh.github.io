---
title: Drone+Github搭建自动化流水线
date: 2021-07-30 10:31:53
tags: 日常折腾
---



### 一、前言

>最近开始写一个自用网盘的小项目，准备会一直去做维护更新，正好去了解下这个做CI/CD的整个流程，随便记录下第一次搭建过程



### 二、查看官网

第一步的话还是先看看Drone的官方文档，地址：https://docs.drone.io/

在上面看的到有结合Github的安装向导，打开就可以按照上面进行。

![image-20210730104140059](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730104140059.png)





![image-20210730104530255](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730104530255.png)



### 三、工具准备

首先是需要安装docker，我这里会使用docker-compose进行编排，所以需要安装docker-compose

{% note info no-icon %}

我这边环境是CentOS7.6

{% endnote %}

#### 3.1 准备docker

```
# 查看电脑上已安装的docker
yum list installed | grep docker
```

![image-20210730105925424](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730105925424.png)

我这边已经安装了，没安装使用下面命令进行安装

```
# 安装
yum -y install docker
# 查看版本
docker -v
```

![image-20210730110128790](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730110128790.png)

其他关于docker的了解，可以看这篇 https://yournotes.cn/2021/08/01/Docker%E5%9F%BA%E7%A1%80/

#### 3.2 准备docker-compose

这里只介绍一个安装方式，也是比较方便快捷的。直接本地到github选择docker-compose版本

地址：https://github.com/docker/compose/releases

![image-20210730110953041](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730110953041.png)

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

选择版本修改指令里的版本号即可（1.29.2），也可直接下载传到服务器

```
# 赋予执行权限
sudo chmod +x /usr/local/bin/docker-compose
```



### 四、环境准备

使用docker-compose安装Drone之前，先准备Drone服务的域名这个需要在配置Github认证的时候写入，只有这样在搭建好后域名访问Drone才会触发Github认证。

#### 4.1 配置Drone服务域名

![image-20210730113950852](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730113950852.png)

我这边就添加个drone.yournotes.cn的解析。

#### 4.2 配置Github认证

在设置里面有个开发者设置选中，

![image-20210730114424028](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730114424028.png)

在OAuth Apps当中即可配置认证应用，我这边已经添加了

![image-20210730114526670](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730114526670.png)

打开New OAuth App里面就几样东西

![image-20210730115229870](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730115229870.png)



注册之后会有Client ID与Client secrets，复制保存下来之后在配置Drone会用

![image-20210730115402287](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730115402287.png)

#### 4.3 配置RPC密钥

这个是Drone的runner与server交互需要配置的，通过`openssl rand -hex 16`指令生成密钥，在之后两边配置一样即可

![image-20210730120101158](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730120101158.png)



### 五、开始安装

先截取文档上的安装方式，如下图。它是使用docker进行拉取镜像然后带环境变量启动。下面我这边就用docker-compose进行统一编排。

**Server安装**

![image-20210730141634508](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730141634508.png)

![image-20210730141659777](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730141659777.png)

**Runner安装**

![image-20210730142249483](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730142249483.png)

![image-20210730142320113](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730142320113.png)

按照文档上这样也可以，分别用docker启动配置好服务地址、密钥等必备的参数。

我这边使用docker-compose，下面分别是.env文件与docker-compose.yml文件。把里面要配置的单独拿出来做了个环境变量的文件.env

将.env文件配置好，再把两个文件传入服务器在同一个文件夹下 执行`docker-compose up -d` 启动即可

```
# .env

DRONE_GITHUB_CLIENT_ID=你的github Client_ID
DRONE_GITHUB_CLIENT_SECRET=你的github Client secrets 
DRONE_RPC_SECRET=你生成的RPC密钥
DRONE_SERVER_HOST=你的Drone服务域名
DRONE_SERVER_PROTO=http （可选https） 
DRONE_RPC_PROTO=http （可选https）
```

```
# docker-compose.yml

version: '2'

services:
  drone-server:
    image: drone/drone:latest
    container_name: drone-server
    networks: 
      - dronenet        # 让drone-server和drone-agent处于一个网络中，方便进行RPC通信
    ports:
      - '10081:80'      # Web管理面板的入口 PROTO=http  时使用该端口
      - '10082:443'     # Web管理面板的入口 PROTO=https 时使用该端口
      - '10083:9000'    # RPC服务端口
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock   # docker.sock [1]
      - /var/data/drone/:/var/lib/drone             # drone数据存放路径
    environment:
      - DRONE_AGENTS_ENABLED=true                   # 使用Runner
      # github相关
      - DRONE_GITHUB_SERVER=https://github.com                    # github的地址
      - DRONE_GITHUB_CLIENT_ID=${DRONE_GITHUB_CLIENT_ID}          # 上一步获得的ClientID
      - DRONE_GITHUB_CLIENT_SECRET=${DRONE_GITHUB_CLIENT_SECRET}  # 上一步获得的ClientSecret
      # 上面三个及下面这三个是必备的
      - DRONE_RPC_SECRET=${DRONE_RPC_SECRET}                      # RPC秘钥	[2]
      - DRONE_SERVER_HOST=${DRONE_SERVER_HOST}                    # RPC域名(在一个实例上可以不用)
      - DRONE_SERVER_PROTO=${DRONE_SERVER_PROTO}                  # git webhook使用的协议(我建议http)
      - DRONE_OPEN=true                                           # 开发drone
      - DRONE_DATABASE_DATASOURCE=/var/lib/drone/drone.sqlite     # 数据库文件
      - DRONE_DATABASE_DRIVER=sqlite3                             # 数据库驱动，我这里选的sqlite
      - DRONE_DEBUG=true                                          # 调试相关，部署的时候建议先打开
      - DRONE_LOGS_DEBUG=true                                     # 调试相关，部署的时候建议先打开
      - DRONE_LOGS_TRACE=true                                     # 调试相关，部署的时候建议先打开
      - DRONE_USER_CREATE=username:TheWinds，admin:true           # 初始管理员用户
      - TZ=Asia/Shanghai                                          # 时区
    restart: always
  drone-agent:
    image: drone/agent:latest
    container_name: drone-agent
    networks: 
      - dronenet     # 让drone-server和drone-agent处于一个网络中，方便进行RPC通信
    depends_on:
      - drone-server 
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # docker.sock [1]
    environment:
      - DRONE_RPC_SERVER=http://drone-server  # RPC服务地址
      - DRONE_RPC_SECRET=${DRONE_RPC_SECRET}  # RPC秘钥
      - DRONE_RPC_PROTO=${DRONE_RPC_PROTO}    # RPC协议(http || https)
      - DRONE_RUNNER_CAPACITY=2               # 最大并发执行的 pipeline 数
      - DRONE_DEBUG=true                      # 调试相关，部署的时候建议先打开
      - DRONE_LOGS_DEBUG=true                 # 调试相关，部署的时候建议先打开
      - DRONE_LOGS_TRACE=true                 # 调试相关，部署的时候建议先打开
      - TZ=Asia/Shanghai
    restart: always
networks:
  dronenet:					# 让drone-server和drone-agent处于一个网络中，方便进行RPC通信

```



#### 六、简单测试

![image-20210730144252616](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730144252616.png)

按照上面成功启动Drone，接下来就可以访问看看了。我这边配置的http是10081端口。所以得加上端口号，注意阿里云上的安全组也要把这些端口放开。

![image-20210730144612574](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730144612574.png)



首次访问应该是如上图，再选中继续就可进行GitHub认证，GitHub认证后会返回到之前在GitHub上填写的地址

![image-20210730145152743](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730145152743.png)

因为我这边是10081端口，因此这里得写成 `drone.yournotes.cn:10081/login` ，否则就找不到了

![image-20210730145349542](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210730145349542.png)



这里选一个项目开启自动流水线，点击一个项目，进入如下页面点保存即可，它将在code仓库配置webhook，当代码仓库更新时会自动执行.drone.yml下的指令

![image-20210802095938681](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802095938681.png)



保存后，我们在代码仓库添加一个.drone.yml的文件，做一个简单的在控制台打印hello world的操作

![image-20210802100545869](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802100545869.png)

steps下就是安排的步骤，使用的镜像是alpine（一个轻量的Linux），使用这个linux执行了echo指令。

之后是做自动化的打包部署，因此步骤会有多个，也会用到maven镜像等，这里只是简单测试下，是否会执行安排的指令。

不出意外的话我们把文件提交，drone流水线就开始自动进行了

![image-20210802101239461](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802101239461.png)

第一步就是clone代码，第二步就是在steps的第一个步骤greeting（alpine执行两个echo）