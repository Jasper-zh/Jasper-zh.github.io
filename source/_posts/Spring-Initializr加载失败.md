---
title: Spring Initializr加载失败
date: 2021-08-11 12:14:45
tags: 日常问题
categories: 
- 日常
- 问题
---

### 前言

> 本来是看下SpringBoot几个基本组件引入的依赖，使用Idea的Spring Initializr进行初始化项目，但从他这个默认的地址去拉取父项目会有网络问题

![image-20210811121907405](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210811121907405.png)

![image-20210811121944013](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210811121944013.png)

### 解决方式

最好就是用自定义地址，改成国内镜像地址，这里用阿里的：https://start.aliyun.com/  , 改完之后就比较快了

![image-20210811122434114](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210811122434114.png)

![image-20210811122451944](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210811122451944.png)

