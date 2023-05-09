---
title: hexo搭建博客
date: 2020-01-13
tags: Hexo
categories: 
- 博客搭建
- hexo
---



# 前言

> 很早之前呢也是写过博客系统[（旧版）](www.yournotes.cn)，那时是学生时代练习java web基础对于前端也是使用基本的三剑客完成配合一下后端。真正的作为个人的博客使用起来还是不舒服当时但也懒得写个。这次选择直接使用hexo做一个长期日常使用的博客系统[（hexo版）](Jasper-zh.github.io)

# Hexo是什么

**Hexo**是基于**nodejs**的，那么我们需要知道**nodejs**是什么

**node.js**是一个运行在**chromeJavascript**运行环境下（俗称GoogleV8引擎）的开发平台，用来方便快捷的创建服务器端网络应用程序。你可以把它理解为一个轻量级的JSP或PHP环境，但是用来开发Web应用的话，有时要便捷很多。

**Hexo**是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Heroku上

#  环境准备

首先到官网安装nodejs

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/01.png)

安装完成后查看nodejs和其包管理器npm的版本

```shell
node -v
npm -v
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/02.png)

`下面可选：我们通过包管理器npm来安装hexo,但由于源是国外的访问有可能慢。可以添加下国内镜像再安装`

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/03.png)

> 执行完出现如图所示即添加完成，当使用cnpm指令就是使用的国内源，也可以使用原先的npm



# 安装hexo

```shell
cnpm install -g hexo-cli
```

执行完成可以查看下版本

```shell
hexo -v
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/04.png)



#  hexo使用

##  初始化博客目录

在本地创建一个目录比如blog作为博客项目工作区

在你选择的目录下执行初始化命令hexo init,在此之前如果你没有安装git那要先把git安装一下

`之后下面的命令如果识别不了hexo指令，可以在指令前面统一加上npx`

```shell
F:\blog>hexo init
```

在blog下就会完成初始化生成初始工程

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/05.png)这样一个静态的博客项目就生成了，可以本地启动看一下初始状态

```
hexo s
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/06.png)

服务开启成功，在浏览器访问一下本地的4000端口。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/07.png)



## 添加文章

执行语句实际上就是在/source/_posts/目录下添加了md文件。你可以去编辑这个md文件

```
hexo new post "文章名"
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/09.png)

写文章通过markdown语法，就简单的写了一点

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/10.png)

编辑完重新生成静态项目，每次变更都要重新生成一下

```java
hexo clean //删除之前的静态文件

hexo g //重新生成新的
    
hexo s //开启服务
    
```

重新访问，在Hello World文章上面就是我们刚刚写的文章。基本博客功能完成了，接下来部署到github上面进行公网访问

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/11.png)





# 部署到github

现在本地可以访问了，由于他只是一个静态项目把它部署在github上就可以做一个公开访问了。这里就用github演示。没有就注册一个然后创建库，作为个人博客部署一定要用  *(你的github用户名).github.io*  来作为库名创建就可以。我的是Jasper-zh.github.io 

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/12.png)

这一步设置username.github.io是为了开启静态服务，页面项目上传到仓库要想直接访问这些静态资源需要开启静态服务。

接下来在blog目录下安装hexo的git部署插件（作用是一键将环境生成的静态页项目放到github当中）`手动push也是一样的`

```shell
cnpm install --save hexo-deployer-git
```

安装完在目录中的_config.yml中配置上部署的方式和地址。比如这里是  git类型、git仓库是github的一个仓库、仓库的分支是master

```shell
deploy:
  type: git
  repository: https://github.com/Jasper-zh/Jasper-zh.github.io.git
  branch: master
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/13.png)

部署工具和部署配置都完成后就可以通过你的库名访问了，基本上呢没有什么问题。一些自定义个性化的配置可以在之后慢慢的改造



### 为啥使用hexo

第一是简单，第二用的比较多生态好真的遇到问题也比较容易查，主题也多



### 我的hexo博客展示

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/14.png)

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/15.png)

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/hexo搭建博客/16.png)

