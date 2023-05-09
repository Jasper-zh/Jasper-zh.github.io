---
title: 右键打开cmd
date: 2021-03-27 13:47:59
tags: 日常问题
categories: 
- 日常
- 折腾
---



### 前言

> 前面再注册表修改了cmd默认地址，后来发现这样并不好，影响了其他地方的使用。还不如配置可以让windows右键在当前位置打开终端.<!--more-->



### 编辑配置

按shift右键只有Powershell，所以需要自己配置cmd的

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/20210327135055.png)

新建文件cmd.txt键入下列内容，@=为右键显示文字

```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\shell\OpenCmdHere]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\Directory\shell\OpenCmdHere\command]
@="cmd.exe /s /k pushd \"%V\""

[HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere\command]
@="cmd.exe /s /k pushd \"%V\""

[HKEY_CLASSES_ROOT\Drive\shell\OpenCmdHere]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\Drive\shell\OpenCmdHere\command]
@="cmd.exe /s /k pushd \"%V\""

[HKEY_CLASSES_ROOT\LibraryFolder\background\shell\OpenCmdHere]
@="在此处打开命令窗口"
"Icon"="cmd.exe"

[HKEY_CLASSES_ROOT\LibraryFolder\background\shell\OpenCmdHere\command]
@="cmd.exe /s /k pushd \"%V\""
```

之后修改后缀为.reg 双击确定 ，注意文本编码是ANSI ，否则提示语乱码。

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210327140159456.png)

### 更正乱码

当然提示乱不乱码都可以使用，已经注册完了是乱码需要改的话也可以。win+r打开注册表

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210327140843526.png)

打开这个地址：`HKEY_CLASSES_ROOT\Directory\Background\shell\OpenCmdHere`

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210327141018722.png)

右键修改即可

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210327141117525.png)

![](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210327141246386.png)