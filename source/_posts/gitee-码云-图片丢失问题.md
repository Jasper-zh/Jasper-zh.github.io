---
title: gitee(码云)图片丢失问题
date: 2020-12-13 10:57:49
tags: 每日问题
---

### gitee(码云)图片丢失


之前申请了博客园想着美化一番，于是换了BNDong的Silence主题，首页搞了一些封面图片存储在gitee<!--more-->

![](https://gitee.com/Jasper-zh/blogImage/raw/master/gitee图片丢失/1.png)

但部分封面是显示不出来的并且提示是302重定向。

![](https://gitee.com/Jasper-zh/blogImage/raw/master/gitee图片丢失/2.png)

恰好在看这个的时候gitee我已经登陆了因此拿这个地址是可以访问到图片的，因此放着没有解决直到今天早上，直接访问提示302图片的地址发现提示大小超过1M的文件需要登陆才能访问然后跳到gitee的登录页。这就是为什么博客封面302的原因（资源存在但重定向）

### 解决方式

无论是gitee还是github它都提供了一个静态对外访问的接口，需要的仓库开启服务中的Gitee Pages。使用它生成的服务地址再去访问仓库下对应文件的路径`https://用户名.gitee.io/仓库名/文件路径`但文件路径不能有中文，原始数据的访问方式可以有中文，但这个是不能的
![](https://gitee.com/Jasper-zh/blogImage/raw/master/gitee图片丢失/3.png)

![](https://gitee.com/Jasper-zh/blogImage/raw/master/gitee图片丢失/4.png)

![](https://gitee.com/Jasper-zh/blogImage/raw/master/gitee图片丢失/5.png)