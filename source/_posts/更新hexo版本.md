---
title: 更新hexo版本
date: 2021-03-27 10:58:44
tags: Hexo
---

### 前言

> 昨天使用vue 3发现node版本太老，但这个版本是hexo需要的版本。只更新node的话，hexo生成的public会全部为空，所以更新下新版本hexo。这次是直接手动更新，重新初始化hexo，因此做手动备份<!--more-->



### 备份关键文件

之前源文件是推送在github，这边先本地把环境重构之后再推送，我们这边就是把个人的配置的部分都备份一下其他都不要。

* .git 可以先复制一份到时候就可以直接提交
* source 不用说文章什么都在里面
* themes 我们配置的主题样式
* .gitignore 其实不用之后新版本的hexo初始化也有
* _config.yml 页面的各种通用属性配置（头像等等）
* README.md 仓库的说明，不是关键的我这边留着 

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327110241.png)

### 准备新hexo

安装14版本node

配置依赖库镜像

```shell
npm config set registry https://registry.npm.taobao.org
```

安装hexo

```shell
npm install -g hexo-cli
```

 查看版本信息

````
hexo -v
````

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327090400.png)

初始化博客

```shell
hexo init myblog
cd myblog
```

安装依赖后测试一下

```shell
npm i
hexo g
hexo s
```

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327110827.png)



### 填充原博客

没有问题，那现在就相当于把我们的备份全都复制过来就可以了

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327110931.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327110939.png)

### 再次使用出错

这时候就出了问题，也是我当时使用命令更新后的问题，因此才采用了重新下载新版hexo初始化这样的方法，既然还是躲不掉这个问题就去解决这个问题呗

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111137.png)

一开始发现只要不放_config.yml进去，其他的没问题

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111145.png)

也就是问题在配置文件上，再看错误信息，我们找到js的那一行看看，看这个config.url是什么于是我把它拼接在错误信息后面

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111155.png)

再次启动看错误信息，这不就是在_config.yml中配置的url么

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111205.png)

尝试去在地址前加上 `http:` 就好了，奇了怪了以前的hexo配置不用加http所以使用正常，更新新的版本后url就不能这么配置了，要写全

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111817.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111824.png)

完美， `.git` 文件夹也复制过去了所以直接push到我们的源文件备份分支。



### 最后

最后还没完，初始的hexo-cli是不会自带git部署插件的，还有其他。可以对比原博客的package.json看自己以前用了哪些要装上的

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327111832.png)

我这边要给新的hexo装上，以下三个也是以前的hexo有用到

* git部署 `npm install hexo-deployer-git --save`
* 百度站点 `npm install hexo-generator-sitemap --save`

* yilia主题目录 `npm i hexo-generator-json-content --save`