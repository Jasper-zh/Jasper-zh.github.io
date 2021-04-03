---
title: 修改cmd初始地址
date: 2021-03-24 12:32:30
tags: 日常问题
---

> 由于自己平时hexo使用的还是比较频繁总是切换麻烦，所以更改一下cmd的默认地址为hexo地址就随便记录下<!--more-->

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210324121749.png)

这是我们cmd的默认地址，换地址有几种方式。这里不用快捷方式的。改开始命令栏cmd进的窗口的地址，而不是某个cmd快捷方式的地址。

#### 打开注册表

win+r 输入 regedit

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210324124524.png)

地址栏键入：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Command Processor

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210324124622.png)

这里我已经创建了AutoRun的配置，开始是没有的。空白处右键新建字符串，名称`AutoRun`数值数据`cd /d 你的地址`

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210324124858.png)

保存后关掉cmd，重新打开就已经更改了地址了

![](https://gitee.com/Jasper-zh/image_host/raw/master/20210324125223.png)



