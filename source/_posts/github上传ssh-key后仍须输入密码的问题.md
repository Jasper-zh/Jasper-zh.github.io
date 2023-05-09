---
title: github上传ssh-key后仍须输入密码的问题
date: 2020-09-06 13:54:35
tags: 日常问题
categories: 
- 日常
- 问题
---

#### github上传ssh-key后仍须输入密码的问题

出现这种问题是本地clone的仓库使用的https的方式，这种方式连接服务就会使用用户名密码的方式。<!--more-->

```shell
git clone https://github.com/name/project.git
```

我们添加了ssh-key之后是希望使用ssh连接，所以要用下面的ssh的clone方式

```shell
git clone git@github.com:name/project.git
```

但往往我们的仓库是已经以https的方式clone下来了，现在想用ssh连接。这里也是可以直接去改配置文件./git/config当中改url这一项把https的链接改为ssh的

```xml
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = git@github.com:xxx/xxx
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "xxx"]
	remote = origin
	merge = refs/heads/xxx
```

