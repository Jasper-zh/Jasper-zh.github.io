---
title: cookies、sessionStorage、localStorage三者有啥区别
date: 2021-10-18 15:11:58
tags: 日常问题
---

## 前言

> 写项目用到了sessionStorage来存sessionid，之前一直都是用的cookie。调试又看到了还有localStorage。之前没有了解过好奇查了查，顺便在这里记录一下。

![image-20211018160818104](https://gitee.com/Jasper-zh/image_host/raw/master/image-20211018160818104.png)



## 区别

<table>
    <tr>
        <th></th>
        <th>cookies</th>
        <th>sessionStorage</th>
        <th>localStorage</th>
    </tr>
    <tr>
    	<td><b>存储空间 </b></td>
        <td>一般浏览器4KB</td>
        <td>一般浏览器5MB</td>
        <td>一般浏览器5MB</td>
    </tr>
    <tr>
        <td><b>存储时效</b></td>
        <td>默认是关闭浏览器失效，但可设置时间就以设置时间为准</td>
        <td>仅在当前标签页，浏览器或者标签关闭则清除</td>
        <td>永久保存，须手动删除</td>
    </tr>
    <tr>
        <td><b>请求携带 </b></td>
        <td>请求时携带cookies在请求头</td>
        <td>不携带</td>
        <td>不携带</td>
    </tr>
    <tr>
        <td><b>可见范围 </b></td>
        <td>同一主域</td>
        <td>当前标签页</td>
        <td>同一主域</td>
    </tr>
</table>


