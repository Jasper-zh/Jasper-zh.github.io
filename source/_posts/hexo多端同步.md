---
title: hexo多端同步
date: 2021-03-27 10:43:51
tags: Hexo
---

### 前言

> 使用Hexo在github搭建的博客，博客作为一个单独的GitHub仓库存在，但是这个仓库只有生成的静态网页文件，并没有Hexo的源文件，如果要换电脑或者在另外的电脑使用，就需要备份hexo环境文件。 <!--more-->



### 本地环境上传

这里就直接使用你静态文件部署的仓库，在这个仓库下用另外一个分支hexo来存，因为我们需要进行拉取和推送的只是我们的源文件，生成的静态文件只是单方向的部署。所以我们把hexo分支设置为默认分支更方便。

![]( https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327082415.png)

刚创建是一个空分支，我们在本地clone下来把.git文件夹放到项目后即可去push我们的源文件到hexo分支，之后我们在另一台电脑要用clone下来即可

hexo项目里是有一个.gitignore的文件的，里面就是过滤git推送的文件，一些是环境文件生成的不用提交的会过滤掉

![]( https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327083239.png)

### 新电脑环境准备

* 安装git
* 安装node
* 拉取博客环境，拉下来是没有node_modules的被过滤了
* 执行 `npm i` 进行安装依赖2.5配置node环境变量到博客目录下的node_modules`F:\WorkSpace\blog\Jasper-zh.github.io\node_modules\.bin`  

测试上面都完成之后，就可使用hexo命令进行测试了

```shell
hexo g
hexo s 
```

### 注意事项

新电脑node版本尽量和最初上传的hexo环境的电脑差不多

新环境或者更改了node环境，hexo命令找不到就重新全局装一下hexo-cli

```powershell
npm install -g hexo-cli 
```

